---
layout:       post
title:        "delay Record"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - ffmpeg
    - record
---

```
    // Read packets from input file and write to output file
    //TODO: 内存如何释放
    AVPacket pkt;
    CircularBuffer<AVPacket> circlePkgBuf(50);
    // AVPacket pkt[600];
    unsigned long mypts = 0;
    unsigned int output_ctx_timebase = output_ctx->streams[(int)AVMEDIA_TYPE_VIDEO]->time_base.den / input_ctx->streams[(int)AVMEDIA_TYPE_VIDEO]->r_frame_rate.num;
    // av_init_packet(&pkt);

    // while(!start_record){
    //     usleep(1000 * 100);
    // }

    bool keyFrameFound = false;

    while(!start_record){
        AVPacket tmppkt;
        av_read_frame(input_ctx, &tmppkt);

        if(circlePkgBuf.isFull()){
            AVPacket unrefPkt;
            circlePkgBuf.dequeue(unrefPkt);
            av_packet_unref(&unrefPkt);
        }

        circlePkgBuf.enqueue(tmppkt);
    }

    while(!circlePkgBuf.isEmpty()){
        AVPacket savePkt;
        circlePkgBuf.dequeue(savePkt);
        if (savePkt.stream_index == (int)AVMEDIA_TYPE_VIDEO) {
            if(!keyFrameFound){
                if(AV_PKT_FLAG_KEY == savePkt.flags){
                    mylog(I, "found Key frame !!!!\n");
                    keyFrameFound = true;
                }else {
                    av_packet_unref(&savePkt);
                    continue;
                }
            }
            // Set the packet's stream index to the output stream
            savePkt.stream_index = video_stream->index;
            savePkt.pts = mypts;
            savePkt.dts = mypts;
            savePkt.duration = output_ctx_timebase ;
            mypts += output_ctx_timebase;
            // mylog(I, "av_read_frame pts[%d]\n",pkt[i].pts);
            // Write the packet to the output file
            
            if (av_interleaved_write_frame(output_ctx, &savePkt) < 0) {
                mylog(E, "Error: Failed to write packet to output file\n");
                return 1;
            }
        }

        av_packet_unref(&savePkt);
    }

    while (av_read_frame(input_ctx, &pkt) == 0) {
        // if(start_record){
            if (pkt.stream_index == (int)AVMEDIA_TYPE_VIDEO) {
                // if(!keyFrameFound){
                //     if(AV_PKT_FLAG_KEY == pkt.flags){
                //         keyFrameFound = true;
                //     }else {
                //         // av_packet_unref(&pkt);
                //         continue;
                //     }
                // }
                // Set the packet's stream index to the output stream
                pkt.stream_index = video_stream->index;
                pkt.pts = mypts;
                pkt.dts = mypts;
                pkt.duration = output_ctx_timebase ;
                mypts += output_ctx_timebase;
                // mylog(I, "av_read_frame pts[%d]\n",pkt.pts);
                // Write the packet to the output file
                
                if (av_interleaved_write_frame(output_ctx, &pkt) < 0) {
                    mylog(E, "Error: Failed to write packet to output file\n");
                    return 1;
                }
            }
        // }

        av_packet_unref(&pkt);
    }
```

