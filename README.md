# async_await_USE
### async await使用
### 本地目录下有一个movie文件夹,里面有电影文件, 根据电影的名称,从豆瓣获取相应的电影封面,存入movie文件下下
```js
var fs = require("fs");
var path = require("path");
var request = require("request");

var moviedir = path.join(__dirname, "movie"), 
    exts = ['.mkv', '.avi', '.mp4', '.rm', '.rmvb', '.wmv'];
var readFiles = function(){
    return new Promise(function(resolve, reject) {
        fs.readdir(moviedir, function(err, files){
            resolve(files.filter((v) => exts.includes(path.parse(v).ext)))
        })
    })
}
var getPic = function(moviename) {
    return new Promise((resolve, reject) => {
        request(`https://api.douban.com/v2/movie/search?q=${encodeURI(moviename)}`, function(err, res, body) {
        var aaa = JSON.parse(body)    
        resolve(JSON.parse(body).subjects[0].images.large);
        })
    })
}
var saveimp = function(moviename, url) {
    request.get(url).pipe(fs.createWriteStream(path.join(moviedir, moviename + '.jpg')))
}
var AA = async () => {  //自调用报错 所以命名
    var arr = await readFiles();
    for (let i of arr) {
        let name = path.parse(i).name;
        var imgurl = await getPic(name)
        saveimp(name, imgurl)
    }
}
AA();
```
