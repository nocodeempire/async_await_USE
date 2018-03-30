# async_await_USE
### async await使用
### 本地目录下有一个movie文件夹,里面有电影文件, 根据电影的名称,从豆瓣获取相应的电影封面,存入movie文件下下
```js
const fs = require('fs');
const path = require('path');
const request = require('request');

const moviedir = path.join(__dirname, 'movie'),
        exts = ['.mkv', '.avi', '.mp4', '.rm', '.rmvb', '.wmv'];

var getFiles = function(){  //先得到movie目录下匹配的文件名
    return new Promise((resolve, reject) => {
        fs.readdir(moviedir, (err, files) => {
            if(err) return console.log(err);
            resolve(files.filter((file) => exts.includes(path.parse(file).ext)));
        })
    })    
}
var getImgUrl =function(moviename) { //根据文件名获取豆瓣图片地址
    return new Promise((resolve, reject) => {
        request(`https://api.douban.com/v2/movie/search?q=${encodeURI(moviename)}`, (err, res, data) =>{
            if(err) return reject(err);
            resolve(JSON.parse(data).subjects[0].images.large)
        })
    })
}

var AA = async () => {
    var files= await getFiles()    //await后面跟promise对象
    console.log(files)
    for(let i of files) {
        let name = path.parse(i).name;
        let imgUrl = await getImgUrl(name);
        request(imgUrl).pipe(fs.createWriteStream(path.join(moviedir, name + '.jpg')));
    }
}
AA()
```
