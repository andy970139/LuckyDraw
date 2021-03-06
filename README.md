# LuckDraw
## 此project為一抽獎網頁程式，其提供建立抽獎活動、查看抽獎活動與查看抽獎記錄功能。  

**project功能需求:**  
- [x]  1. 架構需有前端網頁，後端商業邏輯處理程式，以及儲存結果的資料庫 
- [x]  2. 符合抽獎人數為60,000人
- [x]  3. 依序抽出獎項(每次點擊按鈕)  
- [x]  4. 每次抽獎時間不得超過2秒，抽過不得再抽，每個人抽到的機率需要相同(確保所抽的每一個都是有random過的)**- [x]  
- [x]  5. 可查詢已抽過之獎項人員名單  
      
**加分條件:**  
- [x]  1. 可重複使用，建立每次抽獎活動，輸入人員名單，多少獎項，已及其對應人數等
- [x]  2. 可查詢歷史抽獎活動已及對應抽獎之名單
- [ ]  3. 採用docker當作runtime 
    
 - [x] 為完成項目   
   
## 以下就資料庫、網頁設計與功能進行說明:  
  
**資料庫**  
    `員工資料庫從檔案 worker.csv匯入(共60000人)`  
    `已建立的資料表已匯出檔案 Dump20220523.sql`  
    `資料庫使用MySQL WORKBENCH 帳號密碼為root 1234 資料庫名稱為test`  
      
  **資料表共4個:**
  1. activity:儲存抽獎活動資訊，其欄位有id(系統自動產生)、aid(活動編號，型態為A+4碼數字如:A0001)、prizetype(獎項1~10獎)、num(該活動獎項的抽獎名額)、finished(該活動個獎是否抽完)、date(系統自動產生的時間戳，紀錄活動建立時間)
  2. history:儲存抽獎紀錄，其欄位有hid(系統自動產生)、aid(活動編號)、wid(員工代號)、prize(獎項)、timestamp(系統自動產生的時間戳，紀錄該人員被抽出的時間)    
  3. whitelist:儲存白名單(於建立活動時設定白名單人員，如未設定則默認全員皆可參與該抽獎活動)，其欄位有id(系統自動產生)、aid(活動編號)、wid(員工代號)  
  4. worker:員工名單(由worker.csv產生)，其欄位有id(員工代號，型態為W+6碼數字如:W000001)、name(員工名子)、certifi(設計為權限，未使用)  
      
 資料表關聯如下圖:  
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/er.png)  
 **網頁架構與設計**  
   
 網頁樹狀結構圖如下:  
   
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/web_structure.png)  
   
 * 檔案功能:  
      * `index.html:作為抽獎程式的使用入口，由此進入建立、查看與抽獎、紀錄的主要功能入口`  
      * `建立活動.jsp:提供建立抽獎活動的ui介面，輸入該活動參數，再由後端handle.jsp程式處理建立活動任務`   
      * `activity.jsp:查看已建立的活動，由此選擇要進行抽獎的活動`   
      * `drawui.jsp:提供抽獎的ui介面，按下該獎項抽獎後，由後端handle.jsp程式進行活動資訊與白名單查詢並抽出獎項，呈現該次抽獎名單，並存入紀錄`   
      * `history.jsp:查看已進行抽獎或是完成抽獎的活動，選擇要查看紀錄的特定活動介面`    
      * `handle.jsp:處理後端新增活動、查詢活動、白名單資訊、並建立抽獎紀錄的後端程式(任務參數由get傳入，內容參數由post傳入)`         
      * `historyui.jsp:該活動目前已抽出的名單與時間`   

  **網頁後端與介面設計:**  
    
   **後端控制程式**
     
 * 後端控制程式主要有handle.jsp與historyui.jsp  
      * handle.jsp  
       `判斷get傳送的任務(add建立活動、draw抽獎)，只有這兩種參數才會進行資料庫連線。` 
         
        判斷建立活動邏輯:   
       1. 若每個獎項皆為0人(若獎項名額未輸入默認為0)為無效建立活動，將直接返回首頁。  
       2. 判斷白名單，白名單為'，'分隔的字串參數，將使用者輸入的白名單與對應的該活動代號存入白名單資料庫。  
       3. 若為有效抽獎活動，建立活動資料，並自動排除沒有名額的獎項，亦即不需要每個獎項皆有名額。  
       4. 建立完成後，提醒使用者並返回首頁。  
         
        執行抽獎:   
       1. 判斷該活動是否有設定白名單，如未設定則從所有員工中抽獎。       
       2. 將該獎項中獎名單存入資料庫。       
       3. 將該活動獎項資料庫設為完成(finished='Y')。        
       4. 顯示該獎項中獎名單。 
         
      *  historyui.jsp  
       `1. 從資料庫查詢中獎紀錄名單與員工表對應的名子。`  
       `2. 顯示已進行抽獎的抽獎活動名單紀錄。`  
       
       
       
  **網頁介面設計:**  
  頁面共同功能設計有:左上角logo提供返回首頁連結，列表提交介面右下角皆為返回鍵，網頁下滑右下角會出現返回頂端懸浮按鈕提供使用者快速回到頂端。  
  
  ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/index2.png)  
(首頁設計 index.html)  
  
  
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/create.png)  
(建立活動頁面設計)  
  
  
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/activity.png)  
(活動選單頁面設計 activity.jsp)  
  
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/drawui.png)  
(抽獎頁面設計 drawui.jsp)  
    
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/draw.png)  
(中獎名單介面 handle.jsp產生的畫面)   
    
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/history.png)  
(抽獎記錄選單介面設計 history.jsp)   
    
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/historyui.png)  
(抽獎記錄介面 historyui.jsp)  
    
    
 ![image](https://github.com/andy970139/LuckyDraw/blob/main/readme/historyui2.png)  
(抽獎記錄介面 historyui.jsp)

      
