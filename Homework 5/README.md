# 電腦幫你看圖 - MS Cognitive Services Computer Vision API 
***
 #### [說明](https://github.com/41071119H-Irene/LAT/blob/main/Homework%205/README.md#%E8%AA%AA%E6%98%8E-1)
 #### [實際互動結果](https://github.com/41071119H-Irene/LAT/blob/main/Homework%205/README.md#%E5%AF%A6%E9%9A%9B%E4%BA%92%E5%8B%95%E7%B5%90%E6%9E%9C-1)
 #### [重點code 回顧](https://github.com/41071119H-Irene/LAT/blob/main/Homework%205/README.md#%E9%87%8D%E9%BB%9E-code-%E5%9B%9E%E9%A1%A7)
 #### [限制](https://github.com/41071119H-Irene/LAT/blob/main/Homework%205/README.md#%E9%99%90%E5%88%B6)
***
## 說明
### 功能
- 輔助學生辨識影片或照片有沒有品牌業配，培養媒體識讀能力。
- 經由活動讓學生思考業配是否會讓媒體有特定立場造成偏頗。

## 實際互動結果 
- 網頁介面
```
💡輸入圖片後，左邊顯示電腦看圖偵測的結果(json格式)，右邊顯示偵測目標圖片
```
![image](https://github.com/41071119H-Irene/LAT/assets/112916890/38624d7a-ea09-44b2-a85f-0f44a9593639)

- 電腦偵測: 有可能業配「7-11」/ 人類偵測: 有可能業配「7-11」 >> **偵測成功**
```
💡圖片下方顯示: 「這個可能有__(品牌名稱)__業配」
```
![image](https://github.com/41071119H-Irene/LAT/assets/112916890/2fbf4419-9702-492c-835f-01eeb58a5379)
- 電腦偵測: 沒有業配/ 人類偵測: 有可能業配「柴語錄」 >> **偵測失敗**
![image](https://github.com/41071119H-Irene/LAT/assets/112916890/30b51236-de04-4b78-a58a-1a6f4d8d2d39)
```
💡圖片下方顯示: 「沒有偵測到業配」
💡偵測失敗可能原因: 因「柴語錄」是本土品牌，電腦資料庫裡面可能沒有此品牌。
```
- 電腦偵測: 沒有業配/ 人類偵測: 沒有業配 >> **偵測成功**
![image](https://github.com/41071119H-Irene/LAT/assets/112916890/0fd013e6-b39e-49cd-a305-722ab671f944)


## 重點 code 回顧
- 分析code
```
function processImageFile(imageObject) {
    
    //確認區域與所選擇的相同或使用客製化端點網址
    var url = "https://eastus.api.cognitive.microsoft.com/";
    var uriBase = url + "vision/v2.1/analyze";
    // 主要使用偵測「Brands(品牌)」工具，運用「Catagories」、「Description」工具輔助確認有沒有順利切換到下一張圖片
    var params = {
        "visualFeatures": "Categories,Brands,Description",
        "details": "",
        "language": "en",
};
```
- 顯示反應code
```
.done(function(data) {
        //顯示JSON內容
        $("#responseTextArea").val(JSON.stringify(data, null, 2));
        $("#picDescription").empty();
        //有沒有偵測到業配都有反應
        if (data.brands && data.brands.length > 0) {
            $("#picDescription").append("這個可能有" + data.brands[0].name + "業配");
        } else {
            $("#picDescription").append("沒有偵測到業配");
        }
})
```
## 限制
- 對外國品牌比較熟悉(因為此API是外國人做的)
- 不確定它認識甚麼品牌(它居然不認識YouTube?!)
- 只會辨識logo，還不會辨識相關文字
- 仍有辨識錯誤的情況(大約30-40%)
```
💡以現在來說還不可使用/參考，但如未來有更好的辨識能力的話，對於媒體識讀教學非常有幫助。
```







