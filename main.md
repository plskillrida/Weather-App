# Weather-App
# A Weather App that uses OpenWeatherMap API to show results 
import tkinter as tk
from tkinter import messagebox, Toplevel
import requests
import ttkbootstrap
from PIL import Image, ImageTk
import pymysql
from tkinter import ttk, Tk, StringVar, messagebox


def fetch_weather_data():
    year = year_var.get()
    scity = scity_var.get()

    if not year or not scity:
        result_var.set("Please enter both year and city.")
        return

    connection = None

    try:
        connection = pymysql.connect(
            host='localhost',
            user='root',
            password='Password@123',
            database='weather'
        )
        cursor = connection.cursor()

        query = f"SELECT avg_rain, avg_temp, avg_windspeed FROM record WHERE year = {year} AND cityname = '{scity}'"
        cursor.execute(query)
        data = cursor.fetchone()

        if data:
            sqlresult = (
                f"Year: {year}\nCity: {scity}\nAverage Rainfall: {data[0]}\nAverage Temperature: {data[1]}\nAverage Windspeed: {data[2]}")
            sql_result(sqlresult)

        else:
            result_var.set(f"No data found for {scity} in {year}.")
            messagebox.showwarning("Data Not Found", f"No data found for {scity} in {year}.")

    except pymysql.Error as err:
        result_var.set(f"Error: {err}")
        messagebox.showerror("Error", f"Error: {err}")

    finally:
        if connection and connection.open:
            cursor.close()
            connection.close()

def sql_result(sqlresult):
    sql_window = Toplevel(root)
    sql_window.title("Historical Weather Data")

    sql_label = ttk.Label(sql_window, text="Historical Weather Data for the prompted city", font="Helvetica, 20")
    sql_label.pack(padx=10, pady=20)

    subsql_label = ttk.Label(sql_window, text=sqlresult, font="Helvetica")
    subsql_label.pack(padx=10, pady=60)

def get_weather(city):
    API_key = "f9bd5dc2a3d4a35c1d1c2f449d179862"
    url = f"https://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_key}"
    res = requests.get(url)

    if res.status_code == 404:
        messagebox.showerror("Error", "City not found")
        return None

    weather = res.json()
    icon_id = weather['weather'][0]['icon']
    temperature = weather['main']['temp'] - 273.15
    fl = weather['main']['feels_like'] - 273.15
    description = weather['weather'][0]['description']
    humidity = weather['main']['humidity']
    pressure = weather['main']['pressure']
    visibility = weather['visibility']
    speed = weather['wind']['speed']
    clouds = weather['clouds']['all']
    city = weather['name']
    country = weather['sys']['country']

    icon_url = f"https://openweathermap.org/img/wn/{icon_id}@2x.png"
    result_text = (
        f"{city}, {country}\n"
        f"Temperature: {temperature:.2f}°C\n"
        f"Feels Like: {fl:.2f}°C\n"
        f"Description: {description}\n"
        f"Humidity: {humidity}%\n"
        f"Pressure: {pressure} hPa\n"
        f"Clouds: {clouds} %\n"
        f"Visibility: {visibility} m\n"
        f"Wind Speed: {speed} m/s"
    )

    return result_text, icon_url

def show_result_window(result, icon_url):
    result_window = Toplevel(root)
    result_window.title("Weather Data")

    heading_label = ttk.Label(result_window, text="Current Weather Data for the prompted city", font="Helvetica, 60")
    heading_label.pack(padx=10, pady=20)

    image = Image.open(requests.get(icon_url, stream=True).raw)
    icon = ImageTk.PhotoImage(image)
    icon_label = ttk.Label(result_window, image=icon)
    icon_label.image = icon
    icon_label.pack()

    result_label = ttk.Label(result_window, text=result, font="Helvetica")
    result_label.pack(padx=10, pady=60)

def search():
    city = city_entry.get()
    result, icon_url = get_weather(city)
    if result:
        show_result_window(result, icon_url)


root = ttkbootstrap.Window(themename='morph')
root.title("🌦Indra")
root.geometry('1000x800')
icon_image = Image.open('C:\\Rida\\Indra\\Indra\\logo.png')
icon_photo = ImageTk.PhotoImage(icon_image)

root.iconphoto(True, icon_photo)

l1 = tk.Label(root, text="𝙸𝚗𝚍𝚛𝚊 : 𝙰 𝚆𝚎𝚊𝚝𝚑𝚎𝚛 𝙰𝚙𝚙", font="Helvetica, 25")
l1.pack()

city_entry = ttkbootstrap.Entry(root, font="Helvetica, 14")
city_entry.pack(padx=5, pady=10)

search_button = ttkbootstrap.Button(root, bootstyle="info", text="𝚂𝚎𝚊𝚛𝚌𝚑", command=search)
search_button.pack(pady=10)

d = tk.Label(root, text="-------------------------------------------------------------------------------------------------", font="Helvetica, 12")
d.pack()

l2 = tk.Label(root, text="𝙷𝚒𝚜𝚝𝚘𝚛𝚒𝚌𝚊𝚕 𝚠𝚎𝚊𝚝𝚑𝚎𝚛 𝚍𝚊𝚝𝚊 (𝚊𝚟𝚎𝚛𝚊𝚐𝚎 𝚛𝚊𝚒𝚗𝚏𝚊𝚕𝚕, 𝚊𝚟𝚎𝚛𝚊𝚐𝚎 𝚝𝚎𝚖𝚙𝚎𝚛𝚊𝚝𝚞𝚛𝚎, 𝚊𝚟𝚎𝚛𝚊𝚐𝚎 𝚠𝚒𝚗𝚍𝚜𝚙𝚎𝚎𝚍) 𝚛𝚎𝚝𝚛𝚒𝚎𝚟𝚊𝚕 𝚏𝚘𝚛" 
                         "𝙱𝚊𝚗𝚐𝚊𝚕𝚘𝚛𝚎, 𝙲𝚑𝚎𝚗𝚗𝚊𝚒, 𝙳𝚎𝚕𝚑𝚒, 𝙼𝚞𝚖𝚋𝚊𝚒, 𝙷𝚢𝚍𝚎𝚛𝚊𝚋𝚊𝚍 𝚏𝚛𝚘𝚖 𝟸𝟶𝟷𝟾-𝟸𝟶𝟸𝟸" , font="Helvetica, 13")
l2.pack()

year_label = ttk.Label(root, text="𝙴𝚗𝚝𝚎𝚛 𝚈𝚎𝚊𝚛")
year_label.pack(padx=10, pady=10)

year_var = StringVar()
year_entry = ttk.Entry(root, textvariable=year_var)
year_entry.pack(padx=10, pady=10)

city_label = ttk.Label(root, text="𝙴𝚗𝚝𝚎𝚛 𝙲𝚒𝚝𝚢")
city_label.pack(padx=10, pady=10)

scity_var = StringVar()
scity_entry = ttk.Entry(root, textvariable=scity_var)
scity_entry.pack(padx=10, pady=10)

fetch_button = ttk.Button(root, bootstyle="primary", text="𝙵𝚎𝚝𝚌𝚑 𝙳𝚊𝚝𝚊", command=fetch_weather_data)
fetch_button.pack(pady=10)

result_var = StringVar()
result_label = ttk.Label(root, textvariable=result_var, wraplength=300)
result_label.pack(padx=10, pady=10)

root.mainloop()
