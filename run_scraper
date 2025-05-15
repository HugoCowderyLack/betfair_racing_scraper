from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.firefox.options import Options
import time
import pandas as pd
import warnings

df = pd.DataFrame(columns=['Month','Day','Track','Race','Position','BSP','TT','Place']).set_index(['Month','Day','Track','Race'])
warnings.simplefilter(action='ignore', category=FutureWarning)

def go_to_greyhounds(driver):
    try:
        dog_button = driver.find_element(By.ID, 'greyhound')
        dog_button.click()
    except NoSuchElementException:
        print("Greyhounds button not found")

def select_day(driver, day):
    try:
        open_calendar(driver)
        time.sleep(2)  # adjust this delay as needed

        day_button = driver.find_element(By.XPATH, f"//div[@class='flatpickr-rContainer']//span[@class='flatpickr-day ' or 'flatpickr-day selected'][not(contains(@class, 'flatpickr-day prevMonthDay'))][text()='{day}']")  # Replace with actual identifier
        print(day_button)
        day_button.click()
        time.sleep(10)  # adjust this delay as needed
    except NoSuchElementException:
        print("Day button not found")

def open_calendar(driver):
    try: 
        calendar_button = driver.find_element(By.CLASS_NAME, 'calendar-image')  # Replace with actual identifier
        calendar_button.click()
    except NoSuchElementException:
        print("Calendar button not found")
"""
def prev_month(driver): #assumes calendar is open, clicks on the previous month button
    try: 
        prev_month_button = driver.find_element(By.CLASS_NAME, 'flatpickr-prev-month')
        prev_month_button.click()
    except NoSuchElementException:
        print("Prev month button not found")
""" 

def get_month(driver):
    try: 
        open_calendar(driver)
        time.sleep(2)
        month_elem = driver.find_element(By.CLASS_NAME, 'cur-month')
        month = month_elem.text
        open_calendar(driver)
        time.sleep(2)
        return month
    except NoSuchElementException:
        print("Calendar button not found")
    
def get_race_data(meeting):
    try:
        # gets all of the data for a specified race
        race_data = meeting.find_element(By.XPATH, ".//div[@class='betfair-url'][not(contains(@style, 'display: none;'))]")
        
        # sorts the data into lists
        BSP_elements = race_data.find_elements(By.XPATH, ".//div[@class='price win']")
        TT_elements = race_data.find_elements(By.XPATH, ".//div[@class='price best-tote']")
        PO_elements = race_data.find_elements(By.XPATH, ".//div[@class='price place']")
        pos_elements = race_data.find_elements(By.XPATH, ".//div[@class='place-title']")

        # get text from element
        BSPs = [elem.text for elem in BSP_elements]
        TTs = [elem.text for elem in TT_elements]
        POs = [elem.text for elem in PO_elements]
        positions = [elem.text for elem in pos_elements]
    
        # Filter out empty strings
        BSPs = list(filter(None, BSPs))
        TTs = list(filter(None, TTs))
        POs = list(filter(None, POs))
        positions = list(filter(None, positions))    
        
        # prints data
        print(BSPs)
        print(TTs)
        print(POs)
        print(positions)
        
        return BSPs, TTs, POs, positions

    except NoSuchElementException:
        print("Odds element not found")
        return None, None, None, None
    
def main():

    days = ['1', '2', '3', '4', '5', '6', '7', '8', '9','10' ,'11', '12', '13', '14','15','16', '17', '18', '19','20' ,'21', '22', '23', '24', '25','26', '27', '28', '29', '30', '31']  # fill this in
    
    #   

    main_url = 'https://www.betfair.com.au/hub/racing/horse-racing/racing-results/'  # replace with your actual URL
    driver.get(main_url)
    time.sleep(8)
    data_rows = []
    
    month = get_month(driver)
    print(f'Current month: {month}')  # Debugging print statement
    for day in days:
        
        # go to correct day
        select_day(driver, day)
        print(day)
        
        # go_to_greyhounds(driver) #comment/uncomment to do horsies/doggies
        
        # get tracks
        meeting_list = driver.find_element(By.CLASS_NAME, "filters-list")
        tracks = meeting_list.find_elements(By.XPATH, ".//div[not(contains(@style, 'display: none;'))]//div[@class='meeting-name']")  # find all track elements
        
        for track in tracks:
            
            track_name = track.text
            print(track_name)
            
            track.click()  # open the track
            time.sleep(2)

            meeting = driver.find_element(By.XPATH, "//div[@class='meeting'][not(contains(@style, 'display: none;'))]")
            races = meeting.find_elements(By.XPATH, ".//div[@class='race-number']")  # find all race elements
            race_num = 1

            for race in races:

                race.click()  # open the race
                time.sleep(2)
                
                #get data
                BSPs, TTs, POs, positions = get_race_data(meeting)

                # Use the zip function to combine the two lists
                for bsp, tt, po, pos in zip(BSPs, TTs, POs, positions):
                    # Append the pair to the DataFrame
                    new_row = {'Month': month, 'Day': day, 'Track': track_name, 'Race': race_num, 'Position': pos, 'BSP': bsp, 'TT': tt, 'Place': po}
                    data_rows.append(new_row)
                race_num += 1
                
    global df
    df = pd.DataFrame(data_rows).set_index(['Month', 'Day', 'Track', 'Race'])
    display(df)

    driver.quit()  # Close the driver

    #collects and formats data into df
    df['Position'] = df.groupby(['Month','Day','Track','Race']).cumcount() + 1
    df['Position'] = df['Position'].replace('-', '0')
    df['Position'] = df['Position'].replace(r'\D', '', regex=True).astype(int)
    df['Race'] = df['Race'].astype(int)
    df= df.set_index(['Month','Day','Track','Race'])
    df.tail(100000)

    #convert to excel file with the given file name
    df.to_excel('MARCH23.xlsx')

if __name__ == "__main__":
    main()
