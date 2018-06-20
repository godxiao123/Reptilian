from selenium import webdriver
import requests
from lxml import etree
from time import sleep
import os
class baidutieba:
    def __init__(self,name,passward):
        self.url="http://tieba.baidu.com/f?kw=python&fr=ala0&tpl=5"
        self.user_agent={
        'User-Agent:Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36'}
        self.username=name
        self.passward=passward
        self.yuanma=''
        self.newdata=''
        self.picturepath = 'G:\Crawler\picture'
        self.fateiren=[]
        self.url1=[]
        self.url2=[]
        self.newurl=[]
        self.huifuren=[]
        self.all = []
        self.all2=[]
        self.newurl1=[]
        self.allname=[]
        self.allimg=[]
        self.allname1=[]
        self.allimg1=[]
    def getweb(self):
        driver = webdriver.Chrome()
        driver.implicitly_wait(3)
        driver.get("http://tieba.baidu.com/f?kw=python&fr=ala0&tpl=5")
        driver.find_element_by_xpath('//*[@id="com_userbar"]/ul/li[4]/div/a').click()
        driver.find_element_by_xpath('//*[@id="TANGRAM__PSP_11__footerULoginBtn"]').click()
        username = driver.find_element_by_name("userName")
        password= driver.find_element_by_name("password")
        username.send_keys(self.username)
        password.send_keys(self.passward)
        driver.find_element_by_xpath('// *[ @ id = "TANGRAM__PSP_11__submit"]').click()
        sleep(3)
        self.yuanma=driver.page_source
        self.newdata=etree.HTML(self.yuanma)
#发帖人xpath
        for i in range(1,53):
            self.fateiren=self.newdata.xpath('//*[@id="thread_list"]/li[{}]/div/div[2]/div[1]/div[2]/span[1]/span[1]/a/@href'.format(i))
            self.all.append(self.fateiren)
        while [] in self.all:
             self.all.remove([])

#获取发帖人链接
        for i in self.all:
             j="".join(i).strip('/home/main/?un=&ie=utf-8&fr=fr')
             self.url1.append(j)
        for j in self.url1:
             new='http://tieba.baidu.com/home/main/?un={}&ie=utf-8&fr=frs'.format(j)
             self.newurl.append(new)
#发帖人头像、昵称
        for i in self.newurl:
            webdata=requests.get(i).content
            lxml=etree.HTML(webdata)
            name="".join(lxml.xpath('//*[@id="userinfo_wrap"]/div[2]/div[2]/span/text()'))
            self.allname.append(name)
            img="".join(lxml.xpath('//*[@id="j_userhead"]/a/img/@src'))
            self.allimg.append(img)
        while '' in self.allname:
             self.allname.remove('')
        while '' in self.allimg:
            self.allimg.remove('')
#回复人xpath
        for i in range(2,53):
            self.huifuren=self.newdata.xpath('//*[@id="thread_list"]/li[{}]/div/div[2]/div[2]/div[2]/span[1]/a/@href'.format(i))
            self.all2.append(self.huifuren)
#获取回复人链接
        for i in self.all2:
             j="".join(i).strip('/home/main/?un=&ie=utf-8&fr=fr')
             self.url2.append(j)
        #print(self.url1)
        for j in self.url2:
             new=('http://tieba.baidu.com/home/main/?un={}&ie=utf-8&fr=frs'.format(j))
             self.newurl1.append(new)
#回复人头像、昵称
        for i in self.newurl1:
            webdata=requests.get(i).content
            lxml=etree.HTML(webdata)
            #print(webdata)
            name1="".join(lxml.xpath('//*[@id="userinfo_wrap"]/div[2]/div[2]/span/text()'))
            img1="".join(lxml.xpath('//*[@id="j_userhead"]/a/img/@src'))
            self.allname1.append(name1)
            self.allimg1.append(img1)
        while '' in self.allname1:
             self.allname1.remove('')
        while '' in self.allimg1:
            self.allimg1.remove('')
#保存发帖人头像、昵称
        for i,j in zip(self.allname,self.allimg):
            print(i)
            with open(self.picturepath + os.sep + '{}.jpg'.format(i), 'wb') as f:
                    print(j)
                    if j is not None:
                        img = requests.get(j).content
                        f.write(img)
                    else:
                        print ('error url : ' + j)
            sleep(1)
#保存回复人头像、昵称
        for i, j in zip(self.allname1, self.allimg1):
            with open(self.picturepath + os.sep + '{}.jpg'.format(i), 'wb') as f:
                if j is not None:
                    img = requests.get(j).content
                    f.write(img)
                else:
                    print ('error url : ' + j)
if __name__=="__main__":
    name=input("输入贴吧用户名：")
    passward=input("输入密码")
    baidu=baidutieba(name,passward)
    baidu.getweb()




