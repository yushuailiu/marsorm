## 什么是MarsORM
MarsORM是一个golang ORM库，目标是成为一个友好并强大的ORM库。
## 支持的语法与特性
1. 多个数据连接，负载均衡，支持读写分离
2. 支持Hook(create、query、delete等)
3. 支持查询事件监听，可以用来记录所有的查询语句
4. 数据库迁移
5. Model自动生成

```go

// 新建
db.Insert(&user)
db.InsertGetId(&user)
db.Table("users").Insert(map[string]interface{}{
    "id" => 1,
    "name" => test,
})
// 查询
// 获得所有符合条件的数据
db.Table("users").Get(&users)
// 获得第一个符合条件的
db.Table("users").First(&user)
// Table可省略时读取User配置的表名
db.First(&user)
db.FirstOrInsert(&user)
db.Get(&users)
// 最后一个
db.Last(&user)
// 去重
db.Distinct().Get(&users)
// 选择字段
db.Select("id", "name as user_name").AddSelect().Get(&users)

db.Where("id", ">", 1).Get(&users)
db.Where("name", "like", "liushuai%").Get(&users)
db.OrWhere("id", 1).Get(&users)
db.Where(map[string]interface{}{
	"id": 1,
	"name": "shuai",
}).Get(&users)
db.Where(&User{id:1}).Get(&users)

db.whereIn("id", [1, 2, 3]).Get(&users)
db.Between("id", 1, 10).Get(&users)

db.Order("id desc").Get(&users)
db.Limit(10).Get(&users)
db.Offset(5).Get(&users)
db.Table("users").Count(&count)
db.Table("users").GroupBy("sex","status").Get(&results)
db.Table("users").GroupBy("sex","status").Having("sum(score)", ">", 100).Get(&results)

// 更新
db.First(&user)
user.Name = "test"
user.Save(&user)

db.Model(User{}).where("id", 1).Update(map[string]interface{}{
	"name":"test",
});
db.Model(User{}).where("id", 1).Update(map[string]interface{}{
	"name":"test",
});
db.Model(User{}).where("id", 1).Update("name", "test")

db.Model(User{}).Increment("votes")
db.Model(User{}).Increment("votes", 5)
db.Model(User{}).Increment("votes", 5, map[string]interface{}{
	"name": "test",
})

db.Model(User{}).Where("id", 1).Increment("votes", 5)
db.Model(User{}).Decrement("votes")
db.Model(User{}).Decrement("votes", 5)
db.Model(User{}).Where("id", 1).Decrement("votes", 5)

// 删除
db.Delete(User{})
db.Where("id", 1).Delete(User{})
db.truncate(User{})

// 事务
tx := db.Begin()
tx.Insert(&user)
tx.Insert(&auth)
tx.Commit()

tx.Rollback()

// Raw
db.Raw("select name from user").Scan(&result)
db.Raw("select name from user where id = ?", 11).Scan(&result)

// Exec
db.Exec("drop table user")
db.Exec("update user set status=?", 11)

row := db.Model(User{}).Where("id", 11).Select("id", "name").Raw()
rows := db.Model(User{}).Where("id", ">", 1).Select("id", "name").Raws()

rows, err := db.Raw("select name from user where id > ?", 1)
defer rows.Close()
for rows.Next() {
	rows.Scan(&name)
}

rows, err := db.Model(User{}).Where("id", ">", 1).Select("name").Rows()
defer rows.Close()
for rows.Next() {
	rows.Scan(&name)
}

```