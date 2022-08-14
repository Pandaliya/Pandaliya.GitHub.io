## 在dashboard中操作数据

接入CloudKit，创建Container后，会自动生成一个一个schema来保存数据。schema中定义了你需要保存的数据类型（Record Types）以及他们之间的关系。同时也可以在程序中以使用时生成的方法（just-in-time）的方式创建一个schema。下面的代码就展示了这种方式。

### 创建Records同步到iCloud

使用如下代码创建一条记录并同步到iCloud

```Swift
func saveRecord() {
    // 根据recordType创建一条记录，recordType必须唯一
    let record = CKRecord(recordType: "ToDoItem")
    
    // 定义record的filed
    record.setValuesForKeys([
        "datetime" : Date().description,
        "conent_json_string" : "{\"id\": 1200}",
        "user_idef" : "ckuabdugrekdsad001mnjsajd"
    ])
    
    // 根据identifier获取container
    let container = CKContainer(identifier: "********")

    // 保存记录到私有数据库，该数据库只存在于用户的iCloud
    let database = container.privateCloudDatabase
    database.save(record) { reco, err in
        if err != nil {
            debugPrint("save record error: \(err.debugDescription)")
        } else {
            debugPrint("save record success: ", reco ?? "nil")
        }
    }
}
```

执行完成的代码，如果没有ToDoItem 的record Type将会在Schema中创建该记录类型。如果存在则会添加一条该类型的记录，这可以在Dashboar中看到。
![](https://docs-assets.developer.apple.com/published/aad9e61cf0/rendered2x-1621873584.png)


