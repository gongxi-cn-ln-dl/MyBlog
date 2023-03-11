---
title: 如何快速从博客园搬家.py
date: 2023-02-23 16:31:00
tags: [Python]
mathjax: true
excerpt: false
---

脑袋一热想把博客搬走 用上了 Hexo

但是文都在博客园上，要搬家得全搬啊（~~虽然感觉之前写的也没啥意义~~

遂写了个 python 程序用来搬走 `.md` 格式的文

自动加 Hexo 博客需要的 `Title`、`Date`、`Tags`

```python
import requests
import re
import time
import os

# --------------------------------------------------------------------
# 需要修改的是下面三个变量
# --------------------------------------------------------------------

homePage = 'https://www.cnblogs.com/burnling/' #博客首页 注意最后要有反斜杠
pageNum = 9 #博客总页数
cookie = '填自己的cookie，不然拿不到.md文件'

needMathJax = True #是否开启MathJax
folderName = 'mds'

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36',
    'cookie': cookie
}

def getBlogId():
    res = requests.get(homePage)
    return re.findall('var currentBlogId = (\d*);',res.text,re.S)[0]

def getUrl(text):
    return re.findall('<a class="postTitle2 vertical-middle" href="(.*?)">',text,re.S)[0]

def getTitle(text):
    return re.findall('<span>(.*?)</span>',text,re.S)[0].strip()

def getDate(text):
    date_ = re.findall('<div class="postDesc">(.*?)<',text,re.S)[0]
    date_ = re.findall('(\d{4}-\d{2}-\d{2} \d{2}:\d{2})',date_,re.S)[0]
    date_.strip()
    date_ = date_+':00'
    return date_

def getMarkDownData(url,title):
    headers['referer'] = url

    url = '.'.join(url.split('.')[:-1]+['md'])

    ress = requests.get(url,headers=headers)
    ress.encoding = 'utf-8'

    text = re.sub('#{1,2} *('+title+')','',ress.text)

    return text

def getTags(text):
    params = {
        'postId' : re.findall('p/(\d{7,9}).html',text,re.S)[0],
        'blogId' : getBlogId(),
        '_' : str(int(time.time() * 1000))
    }
    url = f'{homePage}ajax/CategoriesTags.aspx'

    headers['referer'] = text
    res = requests.get(url,params=params,headers=headers)

    aElements = ','.join(re.findall('<a (.*?</a>)',res.text))

    return re.findall('">(.*?)</a>',aElements,re.S)

def get(text):
    url = getUrl(text)

    title = getTitle(text)
    
    print(f"Downloading {title}...")

    date = getDate(text)

    FileData = getMarkDownData(url,title)

    tags = getTags(url)

    mathjax = "\nmathjax: true" if needMathJax else ""

    WriteData = f'---\ntitle: {title}\ndate: {date}\ntags: [{", ".join(tags)}]{mathjax}\n---\n\n{FileData}'
    
    FileName = re.sub('(\[.*?\])','',title)

    with open(f"mds/{FileName}.md","w",encoding='utf-8') as f:
        f.write(WriteData)

    
def main(pages):

    res = requests.get(f'{homePage}?page={pages}')

    result = re.findall('<div id="mainContent">(.*?)<!--end: mainContent 主体内容容器-->',res.text,re.S)[0]
    result = result.split('<div class="day" ro')

    texts = []

    for i in result:
        texts += i.split('class="postSeparator"')
        
    for text in texts:
        try:
            get(text)
        except BaseException as E:
            print(E)

try:
    os.mkdir(folderName)
except:
    pass

for i in range(1,pageNum):
    main(i)


```






