<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>海士町 商店紹介</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>

body{
  font-family:sans-serif;
  margin:0;
  background:white;
}

header{
  background:#2c6e49;
  color:white;
  text-align:center;
  padding:25px;
}

/* 🆕 バージョン表示 */
.version-badge{
  position:fixed;
  top:10px;
  right:10px;
  background:#2c6e49;
  color:white;
  padding:6px 10px;
  border-radius:8px;
  font-size:13px;
  font-weight:bold;
  box-shadow:0 2px 6px rgba(0,0,0,0.2);
  z-index:100;
}

/* 👀 サイト閲覧数 */
.site-view{
  text-align:center;
  font-size:16px;
  padding:10px;
  background:#f5f5f5;
}

/* 🔙 戻るボタン */
.back{
  display:block;
  margin:10px;
  text-decoration:none;
  color:#2c6e49;
  font-weight:bold;
}

/* 🔍 検索 */
.search-box{
  max-width:900px;
  margin:20px auto;
  padding:0 20px;
}

.search-box input{
  width:100%;
  padding:12px;
  font-size:18px;
  border-radius:8px;
  border:1px solid #ccc;
}

/* 📦 コンテンツ */
.container{
  max-width:900px;
  margin:auto;
  padding:20px;
}

.shop{
  margin-bottom:60px;
}

.photo img{
  width:100%;
  border-radius:12px;
  margin-bottom:10px;
}

.map iframe{
  width:100%;
  height:40vh;
  border-radius:12px;
}

</style>
</head>

<body>

<!-- 🆕 バージョン -->
<div class="version-badge">V3.5.1</div>

<header>
<h1>海士町 商店</h1>
</header>

<a href="index.html" class="back">← トップに戻る</a>

<!-- 👀 サイト閲覧 -->
<div class="site-view" id="siteView"></div>

<!-- 🔍 検索 -->
<div class="search-box">
<input id="search" placeholder="検索（漢字・ひらがなOK）">
</div>

<div id="shopList" class="container"></div>

<script>

const API_KEY="KreHOkHj6EARBhfMoBtGdszqZ1xVgQSp1Htq";
const API_URL="https://amatownshops.microcms.io/api/v1/shops";

/* 👀 サイト閲覧 */
let totalViews=parseInt(localStorage.getItem("siteViews")||"0");
let todayData=JSON.parse(localStorage.getItem("siteToday")||"{}");
let sessionFlag=sessionStorage.getItem("visited");

const today=new Date().toISOString().slice(0,10);

if(todayData.date!==today){
  todayData={date:today,count:0};
}

if(!sessionFlag){
  totalViews++;
  todayData.count++;

  localStorage.setItem("siteViews",totalViews);
  localStorage.setItem("siteToday",JSON.stringify(todayData));
  sessionStorage.setItem("visited","true");
}

document.getElementById("siteView").innerHTML=
`👀 総閲覧数：${totalViews}回 ｜ 📅 今日：${todayData.count}回`;

/* データ取得 */
let allShops=[];

fetch(API_URL,{
  headers:{"X-MICROCMS-API-KEY":API_KEY}
})
.then(r=>r.json())
.then(data=>{
  allShops=data.contents;
  render(allShops);
});

/* 表示 */
function render(list){

const el=document.getElementById("shopList");
el.innerHTML="";

if(list.length===0){
  el.innerHTML="<p>店がまだ登録されていません</p>";
  return;
}

list.forEach(s=>{

el.innerHTML+=`

<div class="shop">

<h2>${s.name}</h2>

<div class="photo">
<img src="${s.image1?.url||""}">
<img src="${s.image2?.url||""}">
</div>

<p>${(s.description||"").replace(/\n/g,"<br>")}</p>

<div class="map">${s.map||""}</div>

</div>

`;

});

}

/* 🔍 検索 */
document.getElementById("search").addEventListener("input",function(){

const key=this.value.toLowerCase();

const filtered=allShops.filter(s=>{

const name=(s.name||"").toLowerCase();

const kanas=Object.keys(s)
.filter(k=>k.startsWith("kana"))
.map(k=>(s[k]||"").toLowerCase());

return name.includes(key)||kanas.some(k=>k.includes(key));

});

render(filtered);

});

</script>

</body>
</html>
