#

## Apache 設定

`Apache` 在 `Mac` 中有已經有安裝好 `Apache` 了，所以這邊我並不另外安裝 `Apache`，而且選擇直接使用內建的 `Apache`。

1. 首先第一件事情是先登入 `root` 權限，才可開啟 `Apache`。
   #首先打開終端機，為了避免稍候有任何權限問題，先把執行權限調整成 `root`
   `sudo su -`

2. 開啓 `apache`
   `cmd+space`（叫出 `Spotlight`）
  
   輸入 `$ terminal`（打開終端機）
   輸入 `$sudo apachectl start`（打開 `apache` 服務）
   在瀏覽器輸入 `http://localhost`，看到 `It works` 就表示成功了！

   此時放網站的目錄是系統層級：
   打開 `finder` 按下 `cmd＋shift＋g` 輸入 `/Library/WebServer/Documents/` 就會看到網站所在資料夾了

   緊接著還要再做一下其他設定，`Apache` 預設會讀取 `/Library/WebServer/Documents/` 這個資料內的文件，但是我們不太可能將每次開發完成的網站都丟進這個資料夾做測試，所以這個時候我們就要再做其他設定讓 `Apache` 可以讀到我們設定的資料夾。

3. 建立**使用者層級網站資料夾**並更改權限,首先，要先建立一個資料夾並命名為   Sites，

   ```bash
   #回到自己家目錄
   exit
   cd ~/
   mkdir Sites
   sudo chmod -R 755 Sites
   ```

   到 `/etc/apache2/users` 下確認是否有 **你的使用者名稱.conf** 這個檔案，像我的使用者名稱是 `allen`，就找一下有沒有 `allen.conf` 這個檔案，如果沒有的話就自己建立一個並輸入下方內容。

   ```bash
   sudo nano /etc/apache2/users/使用者名稱.conf
   ```

   ```yml
   <Directory "/Users/allen/Sites/">  
      AllowOverride All  
      Options Indexes MultiViews 
      Require all granted
      #Order allow, deny
      Allow from all  
   </Directory>
   ```

   `sudo chmod 755 /etc/apache2/users/allen.conf` (allen.conf)

4. 以上設定完成後，再繼續編輯 `/etc/apache2/httpd.conf`，將下列文字前的 `#` 移除。

   `sudo nano /etc/apache2/httpd.conf`

   ```yml
   LoadModule authz_core_module libexec/apache2/mod_authz_core.so
   LoadModule authz_host_module libexec/apache2/mod_authz_host.so
   LoadModule userdir_module libexec/apache2/mod_userdir.so

   Include /private/etc/apache2/extra/httpd-userdir.conf
   ```

   上述步驆完成後繼續編輯 `sudo nano /etc/apache2/extra/httpd-userdir.conf` 這個檔案，將下列文件前的 `#` 移除。
   `Include /private/etc/apache2/users/*.conf`

5. 全部完成後再重啟 `Apache`

   ```bash
   sudo apachectl restart
   ```

6. 此時在瀏覽器中輸入 `http://localhost/~username/`，若成功會看到畫面
   測試: `http://localhost/~使用者名稱`

7. 如果畫面出現 Ｆorbidden，請依下面作修正

   `sudo nano /etc/apache2/httpd.conf`

   ```yml
   <Directory />
    AllowOverride all
    Require all granted
   </Directory>

     :
   User 使用者名稱
   Www _www
   ```

8. 設定 `Host`
  
   - 位置在 `/etc/hosts`
     `cd  /etc`

   - 為了之後方便常常設定，不用每次輸入密碼，終端機執行指令 `sudo chmod 777  /etc/hosts` 將權限打開
   - 為了之後方便常常設定，所以建立一個鏈結，終端機執行指令 `ln -s /etc/hosts ~/Documents/hosts`
   - 用 `nano` 打開編輯 `~/Documents/hosts`，將內容調整一下，常用內容如下：
     `nano ~/Documents/hosts`

     ```yml
      :
     127.0.0.1  ios111.com
     ```

9. 或使用虛擬主機方式:
  
   ```bash
   sudo mkdir /Library/WebServer/Documents/allen
   # sudo chown 使用者名稱:群組  /Library/WebServer/Documents/使用者名稱
   sudo chown allen:staff  /Library/WebServer/Documents/allen 
  
   #或使用虛擬主機方式:
   sudo nano /etc/apache2/users/allen.conf
   ```

   ```yml
     <Directory "/Users/allen/Sites/">  
       AllowOverride All  
       Options Indexes MultiViews 
       Require all granted
       #Order allow, deny
       Allow from all  
     </Directory> 
  
     <VirtualHost *:80> 
       DocumentRoot   "/Users/allen/Sites/" 
       ServerName     ios109.wda.edu.tw
       ErrorLog       "/Users/allen/Sites/sites-error_log" 
       CustomLog      "/Users/allen/Sites/sites-access_log" common 
       
       <Directory "/Users/allen/Sites/">  
          AllowOverride All  
          Options Indexes MultiViews 
          Require all granted
          #Order allow, deny
          Allow from all  
        </Directory>  
      </VirtualHost>
   ```
  
   ```bash
   sudo chmod 755 /etc/apache2/users/allen.conf
   sudo apachectl restart
   ```

★.修改 ~/Document/hosts, 增加 127.0.0.1 對 ios109.wda.edu.tw 之解析
  $ nano ~/Document/hosts
    127.0.0.1 ios109.wda.edu.tw






  
  





