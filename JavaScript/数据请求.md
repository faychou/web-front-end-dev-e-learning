# 数据请求
## AJAX

## fetch
``` js
fetch(url, {
  method: 'POST',
  body: Json.stringify(comment)
})
.then((res)=>res.json())
.then((resJson)=>{
  this.setState({comments: resJson})
})
.catch((ex)=>{
  console.error(ex)
})
```