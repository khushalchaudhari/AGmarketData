import time             
import pandas as pd    
from selenium import webdriver     
from selenium.webdriver.chrome.service import Service      
from selenium.webdriver.common.by import By     
from bs4 import BeautifulSoup       
from selenium.webdriver.support.ui import Select      

s = Service('chromedriver.exe')  
driver = webdriver.Chrome(service=s)
driver.implicitly_wait(10)      
driver.maximize_window()       
driver.get("https://agmarknet.gov.in/PriceAndArrivals/DatewiseCommodityReport.aspx")
  
Select(driver.find_element(By.XPATH,"//select[@id='cphBody_cboYear']")).select_by_index(3)      
Select(driver.find_element(By.XPATH,"//select[@id='cphBody_cboMonth']")).select_by_visible_text("June")   
time.sleep(4)       
Select(driver.find_element(By.XPATH,"//select[@id='cphBody_cboState']")).select_by_visible_text("Maharashtra")      
time.sleep(4)       
Select(driver.find_element(By.XPATH,"//select[@id='cphBody_cboCommodity']")).select_by_visible_text("Grapes")     
time.sleep(4)       
driver.find_element(By.XPATH,"//input[@id='cphBody_btnSubmit']").click()        
time.sleep(5)       
content = driver.page_source        
soup = BeautifulSoup(content, "html.parser")        

c1 = driver.find_elements(By.XPATH,"//tr[@align='center']/td[1]")   
c2 = driver.find_elements(By.XPATH,"//tr[@align='center']/td[2]")   
c3 = driver.find_elements(By.XPATH,"//tr[@align='center']/td[3]")   
c4 = driver.find_elements(By.XPATH,"//tr[@align='center']/td[4]")   
c5 = driver.find_elements(By.XPATH,"//tr[@align='center']/td[5]")   
c6 = driver.find_elements(By.XPATH,"//tr[@align='center']/td[6]")   
c7 = driver.find_elements(By.XPATH,"//tr[@align='center']/td[7]")   

data=[]     
for i in range(len(c1)):        
    table_data = {'Market':c1[i].text,
                  'Arrival_Date':c2[i].text,
                  'Arrivals (Tonnes)':c3[i].text,
                  'Variety':c4[i].text,
                  'Minimum Price(Rs./Quintal)':c5[i].text,
                  'Maximum Price(Rs./Quintal)':c6[i].text,
                  'Modal Price(Rs./Quintal)':c7[i].text}
    data.append(table_data)

df_data = pd.DataFrame(data)        
df_data.to_csv('scratching.csv',index=False)        
driver.quit()
