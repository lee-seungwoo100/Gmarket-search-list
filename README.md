import re
from typing import ItemsView
from numpy import product
from selenium import webdriver
import requests
from bs4 import BeautifulSoup
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By

search_product ='고구마'

options = webdriver.ChromeOptions()
#options.add_argument('headless')
options.add_experimental_option("excludeSwitches", ["enable-logging"])
options.add_argument("--disable-blink-features")
options.add_argument('--disable-blink-features=AutomationControlled')
options.add_argument('user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.84 Safari/537.36')

browser = webdriver.Chrome(options=options) #'./chromdriver.exe'


# 지마켓 특정키워드로 셀레니움 정보창 정보 가져오기

browser.get('https://www.gmarket.co.kr/')
browser.implicitly_wait(10)
browser.find_element(By.CSS_SELECTOR,'#skip-navigation-search > span > input').send_keys(search_product)
browser.find_element(By.CSS_SELECTOR,'#skip-navigation-search > span > input').send_keys(Keys.ENTER)
browser.find_element(By.CSS_SELECTOR,'#region__content-status-information > div > div > div.box__control-area > div.box__sort-control > div.box__sort-control-selected > button').click()
browser.find_element(By.CSS_SELECTOR,'#region__content-status-information > div > div > div.box__control-area > div.box__sort-control.box__sort-control--active > div.box__sort-control-list > ul > li:nth-child(2) > a').click()
browser.implicitly_wait(10)

items = browser.find_elements(By.CSS_SELECTOR,'div.box__component.box__component-itemcard.box__component-itemcard--general div.box__item-container')
for item in items:
    productname = item.find_element(By.CSS_SELECTOR,'div.box__item-title span.text__item').text
    productprice = item.find_element(By.CSS_SELECTOR,'div.box__item-price strong.text.text__value').text
    try:
        productreview = item.find_element(By.CSS_SELECTOR,'div.box__information-score li.list-item.list-item__feedback-count span.text').text[1:-1]
    except:
        pass
    try:
        productbuy = item.find_element(By.CSS_SELECTOR,'div.box__information-score li.list-item.list-item__pay-count span.text').text[2:]
    except:
        pass
    try:
        productsupplier = item.find_element(By.CSS_SELECTOR,'div.box__information_seller span.text__seller').text
    except:
        pass
    try:
        productlink = item.find_element(By.CSS_SELECTOR,'div.box__image > a').get_attribute('href')
    except:
        pass
    print(productname,productprice,productreview,productbuy,productlink)
