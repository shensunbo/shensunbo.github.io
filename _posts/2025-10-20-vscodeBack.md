---
layout:       post
title:        vscode 背景色修改"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - vscode
    - 护眼色
    - vscode背景
---

# 恢复之前的设置
`Settings Sync: Show Synced Data`

# 背景色和字体模板
settings.json
```
// {
//     "workbench.colorTheme": "Atom One Light"
// }



{

    "editor.minimap.enabled": true,
    "C_Cpp.autocomplete": "default",
    "C_Cpp.intelliSenseEngine": "default",
    "C_Cpp.intelliSenseUpdateDelay": 2000,

    "[cpp]":{
        "editor.quickSuggestions": {
            "other": "on",
            "comments": "on",
            "strings": "on",
            
        },
        "editor.suggest.showEnums": true,
        "editor.suggest.showEnumMembers": true,
        "editor.suggest.showWords": true,
        "editor.suggest.showFunctions": true,
        "editor.suggest.showVariables": true,
        "editor.suggest.showValues": true,
    },
    "[c]":{
        "editor.quickSuggestions": {
            "other": "on",
            "comments": "on",
            "strings": "on"
        },
        "editor.suggest.showEnums": true,
        "editor.suggest.showEnumMembers": true,
        "editor.suggest.showWords": true,
        "editor.suggest.showFunctions": true,
        "editor.suggest.showVariables": true,
        "editor.suggest.showValues": true,
    },
    "editor.fontSize": 16,
    "editor.mouseWheelZoom": true,
    "editor.tokenColorCustomizations": {
         "keywords":{
            "fontStyle": "bold",
            "foreground":  "#2e8d03"
         } , 
         "variables":{
            "fontStyle": "bold",
            "foreground":  "#2c00af"
         } ,
         "functions":{
            "fontStyle": "italic bold",
            "foreground":  "#017531"
         } ,
        "strings": "#bb39d8",
        "numbers": {
            "fontStyle": "bold",
            "foreground":  "#a8760a",
         } ,
        "comments":"#264bee",
        
        "types": {
            "fontStyle":"italic bold",
            "foreground":  "#885405"
         } ,
        /*关键字高亮*/
        "textMateRules": [
            {
                "scope": "log.error",
                "settings": {
                  "foreground": "#fb0101"
                }
            },
            {
                "scope": "log.warning",
                "settings": {
                  "foreground": "#cff807",
                }
            },
            {
                "scope": "log.exception",
                "settings": {
                  "foreground": "#cff807",
                }
            },

        ]
    },
    "editor.semanticTokenColorCustomizations": {
        "enabled": true, // enable for all themes
        "rules": {
            "*.static": {
                "foreground": "#6800d0",
                "fontStyle": "bold"
            },
            "property": {   //属性
                "foreground": "#124b9a",
                "fontStyle": "bold"
            },
            "macro": {      //宏
                "foreground": "#ff0000",
                "fontStyle": "bold"
            },
            // "function": {   //函数
            //     "foreground": "#5491e0",
            //     "fontStyle": "bold"
            // },
            "variable.global": { //全局变量
                "foreground": "#a90696",
                "fontStyle": "bold"
            },
            // "variable.local": { //局部变量
            //     "foreground": "#2271da",
            //     "fontStyle": "bold"
            // },
            "enumMember": "#ff0000",//枚举
            "*.local":{
                "foreground":"#0789ca",
            } ,
        }
    },

    "workbench.colorCustomizations": {
        // 写在 Atom One Light  里面则只对该主题有效a
        // "[Sea Green Theme]": 
        // {
            "editor.background": "#c5f3cb",  
            "sideBar.background": "#b8e4be",
            "activityBar.background": "#9cce9c", 
            // 终端前景背景色
            "terminal.foreground" : "#141416",
            "terminal.background" : "#a0ecd9",

            //"editor.selectionHighlightBorder": "#94767c00",
            "editor.selectionHighlightBackground": "#fcde8d81",
            "selection.background": "#00f853",
            "editor.selectionHighlightBorder": "#ff0000",
            "editor.wordHighlightBorder": "#ff0000",
            "editor.selectionBackground": "#00f853",
            "editor.lineHighlightBackground": "#5ff7a6b5",
            "editor.foldBackground": "#9ebfffcd",
            "editorIndentGuide.activeBackground1": "#ff0000",
            "editorBracketMatch.background": "#ff8a05a9",
            "editorBracketMatch.border": "#ff0000",
            "tab.activeBackground": "#c0d9f070",

            "titleBar.activeBackground": "#839eb870",

        // },
        "editorLineNumber.foreground": "#f19012",
    },
    "workbench.statusBar.feedback.visible": false,
    // "workbench.iconTheme": "eq-material-theme-icons-ocean",
    "workbench.startupEditor": "newUntitledFile",

    "highlightwords.colors": [
        { "light": "#eeff00", "dark": "cyan" },
        { "light": "#15ffdc", "dark": "pink" },
        { "light": "#55ff2b", "dark": "lightgreen" },
        { "light": "#ef75ff", "dark": "magenta" },
        { "light": "#ffc675e6", "dark": "cornflowerblue" },
        { "light": "#dfff75da", "dark": "orange" },
        { "light": "#6ffffde9", "dark": "green" },
        { "light": "#6fadffcc", "dark": "red" }                                        
        
    ],
    "highlightwords.box": {
        "light": false,
        "dark": true
    },
    "highlightwords.defaultMode": {
        "default": 0
    },
    
    "highlightwords.showSidebar": {
        "default": true
    },
    //"tabnine.experimentalAutoImports": true,
    "editor.guides.bracketPairs": true,
    "editor.detectIndentation": false,
    // "clang.diagnostic.enable": true,
    // "clang.executable": "clang",
    // "clang.cflags": ["-std=c99"],
    // "automate.useRTextServer": true,
    "editor.tabCompletion": "on",
    "search.followSymlinks": false,
    "todo-tree.tree.autoRefresh": false,
    "[python]": {
    },
    "remote.SSH.remotePlatform": {
        "myVM": "linux",
        "Vxbox": "linux",
        "192.168.56.101": "linux",
        "10.60.26.213": "linux",
        "10.60.26.105": "linux"
    },

    //日志关键字高亮，指定日志文件
    "files.associations": {
        "*main_log*": "log",
        "*.log.*": "log",
    },

    "[log]": {
        "editor.largeFileOptimizations": false,
    },

    //匹配关键字
    "logFileHighlighter.customPatterns": [
        {
            "pattern": "setProperty",
            "foreground": "#d6150e",
            "background": "#929396"
        },

        {
            "pattern": "onHalEvents",
            "foreground": "#af1f1f",
            "background": "#c6c6c8"
        },
        {
            "pattern": "error",
            "foreground": "#ff0000",
            "background": "#c6c6c8"
        },
        {
            "pattern": "fail",
            "foreground": "#ff0000",  // 红色
            "background": "#c6c6c8"
        },
        {
            "pattern": "failed",
            "foreground": "#ff0000",  // 红色
            "background": "#c6c6c8"
        },
        {
            "pattern": "warning",
            "foreground": "#ffff00",  // 黄色
            "background": "#c6c6c8"
        },
        {
            "pattern": "exit",
            "foreground": "#1500ff",  // 黄色
            "background": "#c6c6c8"
        },
    ],
    
    "git.enableSmartCommit": true,
    "cmake.configureOnOpen": true,
    "workbench.editorAssociations": {
        "*.pdf": "default"
    },
    "remote.autoForwardPortsSource": "hybrid",
    "gitlens.views.repositories.autoRefresh": false,
    "git.autofetchPeriod": 180,
    "tabnine.experimentalAutoImports": true,
    "codeium.enableConfig": {
        "*": true
    },
    "codeium.detectProxy": false,
    "C_Cpp.intelliSenseEngineFallback": "enabled",
    "cmake.showOptionsMovedNotification": false,
    "workbench.colorTheme": "Atom One Light",
    "update.mode": "manual",

    // "java.jdt.ls.java.home": "F:\\JDK",

    // "java.configuration.runtimes": [
    //     {
    //       "name": "JavaSE-17",
    //       "path": "F:\\JDK",
    //       "default": true
    //     }
    // ],
    "gitlens.gitCommands.skipConfirmations": [
        "fetch:command",
        "stash-push:command",
        "switch:command"
    ],
    "cloudcode.duetAI.project": "modified-silo-405108",
    "amazonQ.suppressPrompts": {
        "codeWhispererConnectionExpired": true
    },
    "diffEditor.ignoreTrimWhitespace": true,
    "cmake.pinnedCommands": [
        "workbench.action.tasks.configureTaskRunner",
        "workbench.action.tasks.runTask"
    ],
    "jdk.telemetry.enabled": true,
    "files.autoSave": "onFocusChange",
    
}
```
