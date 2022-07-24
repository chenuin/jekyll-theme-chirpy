---
title:  "靜態網頁部落格工具 Ruby + Jekyll 安裝教學"
date:   2020-05-28 08:00:00
categories: [Jekyll]
tags: [安裝教學, Github, HTML, Jekyll, Linux]
---
### 一、安裝Ruby
作業系統為Ubuntu 20，指令如下：
```bash
sudo apt update
sudo apt install ruby-full build-essential zlib1g-dev
```

設定用戶的環境變數，應避免用 root 安裝 Ruby Gems。
```bash
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```


### 二、安裝Jekyll
Ruby中使用Gem作為套件管理的工具，就像是python和pip的關係。安裝套件的方式 `gem install [packageName]`：
```bash
gem install jekyll bundler
```


### 三、建立新專案
新增一個 `myblog` 的專案
```bash
jekyll new myblog
```

在目錄下應該會發現新的資料夾 `/myblog`，大致的內容可以參考一下
```
├── 404.html
├── about.markdown
├── _config.yml
├── Gemfile
├── Gemfile.lock
├── index.markdown
├── _posts
│   └── 2020-05-27-welcome-to-jekyll.markdown
└── _site
    ├── 404.html
    ├── about
    ├── assets
    ├── feed.xml
    ├── index.html
    └── jekyll
```

### 四、開啟服務
進入目錄後，啟動伺服器，預設主機名稱 `localhost` 、埠號 `4000`，可以根據需求設定：
```bash
cd myblog
bundle exec jekyll serve

// or you can ...
bundle exec jekyll serve --host=[ip address] --port=[port number]
```

瀏覽器開啟 [http://localhost:4000] ，就可以看到預設的首頁，安裝就告一段落，可以開始進行開發。

![Jekyll default homepage](/assets/posts/2020-07-26-jekyll-default-homepage.png "Jekyll default homepage")


[http://localhost:4000]: http://localhost:4000
