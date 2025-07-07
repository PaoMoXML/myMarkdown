todo: 

1. 远程评标
   1. 文字质询开发
2. 辽宁项目





```json
{
    "yudingguid":"预定guid",
    "projectguid":"项目guid",
    "ordertype":"预约类型", // 1--政采，2--工程等
    "kaibiaoroomguid":"开标室guid",
    "pingbiaoroomguid":"评标室guid",
    "zsjsroomguid":"资审接收室guid",
    "zspsroomguid":"资审评审室guid",
    "isflow":"是否流标", // 1--不是，2--是
    "isfuping":"是否复评", // 1--不是，2--是
    "projectname":"项目名字",
    "projectno":"项目编号",
    "kaibiaostarttime":"开标开始时间", // yyyy-MM-dd HH:mm:ss
    "kaibiaoendtime":"开标结束时间",
    "pingbiaostarttime":"评标开始时间",
    "pingbiaoendtime":"评标结束时间",
    "realitypingbiaoendtime":"实际评标结束时间",
    "zsjsstarttime":"资审接收开始时间",
    "zsjsendtime":"资审接收结束时间",
    "biaoduanlist":[
        {
            "biaoduanguid":"标段标识",
            "jianshedanwei":"建设单位",
            "dailiname":"代理名称",
            "biaoduanguid":"标段guid",
            "biaoduanno":"标段编号",
            "biaoduanname":"标段名称",
            "people": [
                "idcard":"身份证",
                "name":"人员姓名",
                "persontype":"人员类型"
            ]
        }
    ],
}
```

```json
{
    "userguid": "标识",
    "yudingguid": "预定guid",
    "projectGuid": "项目guid",
    "name": {
        "param": "姓名（密文）",
        "mode": "国密加解密所需参数",
        "type": " 国密加解密所需参数"
    },
    "shenfenid": {
        "param": "身份证（密文）",
        "mode": "国密加解密所需参数",
        "type": "国密加解密所需参数"
    },
    "zjpic": {
        "mode":"国密加解密所需参数",
        "type":"国密加解密所需参数",
        "param": {"zjpicStr":"照片","datetime":"修改日期"} // 密文
    },
    "zwlist": [{
        "param": {"zhiwenguid":"指纹标识","fingernum":"编号","zhiwenpic":"指纹图片"}, // 密文
        "mode":"国密加解密所需参数",
        "type":"国密加解密所需参数"
    }]
}
```

