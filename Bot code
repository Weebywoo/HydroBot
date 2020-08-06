import json, discord, requests, os, shutil
from discord.ext import commands
from urllib import request
from random import choice
from joke.jokes import *
from io import BytesIO
from PIL import Image

client = commands.Bot(command_prefix = "$")

TOKEN = ""
NASE_API_KEY = ""
animals = ["dog", "cat", "panda", "fox", "bird", "koala", "red_panda"]

hdr = {'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.11 (KHTML, like Gecko) Chrome/23.0.1271.64 Safari/537.11',
       'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
       'Accept-Charset': 'ISO-8859-1,utf-8;q=0.7,*;q=0.3',
       'Accept-Encoding': 'none',
       'Accept-Language': 'en-US,en;q=0.8',
       'Connection': 'keep-alive'}

@client.event
async def on_ready(): print("Bot is ready!")

#@client.command()
#async def help(ctx):await ctx.send("> Commands available:\n> -RandomJoke\n> -ChuckJoke\n> -DadJoke\n> -InspiroBot\n> -Dog\n> -Cat [Status code]\n> -Cheer\n> -APOD\n> -Mars [Rover name] [Camera name] [Date]\n> -RoverCams\n> -Ping\n\n> Explanations:\n> Status codes: 404, 200, 301, . . .(https://www.tutorialspoint.com/http/http_status_codes.htm)\n> Rover names: Curiosity, Opportunity, Spirit\n> Camera names: FHAZ, RHAZ, NAVCAM\n> Dates: e.g. 2019-5-5")

#########################################################################################
#JOKES
@client.command()
async def randomjoke(ctx): await ctx.send(choice([geek, icanhazdad, chucknorris, icndb])())

@client.command()
async def chuckjoke(ctx): await ctx.send(chucknorris())

@client.command()
async def dadjoke(ctx): await ctx.send(icanhazdad())

@client.command()
async def inspirobot(ctx):

    url = "http://inspirobot.me/api?generate=true"
    req = request.Request(url, headers = hdr)
    page = request.urlopen(req)
    content = page.read().decode('utf-8')
    file_name = content.split('/')[-1]
    response = requests.get(content)
    img = Image.open(BytesIO(response.content))
    img.save(file_name)

    await ctx.send(file = discord.File(file_name))
    os.remove(file_name)

@client.command()
async def aimeme(ctx, amount = 1):

    url = "https://api.imgflip.com/get_memes"
    json_url = requests.get(url)
    json_data = json.loads(json_url.text)
    for num in range(amount):

        meme_name = json_data["data"]["memes"][num]["name"]
        meme_url = json_data["data"]["memes"][num]["url"]

        embed = Embed(title = meme_name, description = meme_url)
        embed.set_image(url = meme_url)
        await ctx.send(embed = embed)

@client.command()
async def trump(ctx, user):

    print(user)
    url = "https://api.whatdoestrumpthink.com/api/v1/quotes"
    json_data = json.loads(requests.get(url).text)
    quotes = json_data["messages"]["personalized"]
    number = random.randrange(len(quotes))
    await ctx.send(f"{user} {quotes[number]}")

@client.command()
async def uselessfact(ctx):

    url = "https://uselessfacts.jsph.pl/random.json?language=en"
    jason_data = json.loads(requests.get(url).text)
    await ctx.send(jason_data["text"])

#########################################################################################
#ANIMALS
@client.command()
async def fact(ctx, animal = choice(animals)):

    if animal == "info":
        await ctx.send("There are facts available for Dogs, Cats, Pandas, Foxes, Birds and Koalas")
    else:
        url = f"https://some-random-api.ml/facts/{animal}"
        json_data = json.loads(requests.get(url).text)
        await ctx.send(json_data["fact"])

@client.command()
async def image(ctx, animal = choice(animals)):

    if animal == "info":
        await ctx.send("There are images available for Dogs, Cats, Pandas, Foxes, Birds and Koalas")
    else:
        async with aiohttp.ClientSession() as session:
            async with session.get(f"https://some-random-api.ml/img/{animal}") as request:

                 json = await request.json()
                 image_url = json["link"]
                 file_name = image_url.split('/')[-1]
                 req = requests.get(image_url, stream = True)
                 req.raw.decode_content = True

                 with open(file_name,'wb') as file: shutil.copyfileobj(req.raw, file)
        await ctx.send(file = discord.File(file_name))
        os.remove(file_name)

@client.command()
async def statuscat(ctx, status_code = 404):

    url =  f"https://http.cat/{status_code}.jpg"
    data_name = url.split('/')[-1]
    req = requests.get(url, stream = True)
    req.raw.decode_content = True

    with open(data_name,'wb') as file: shutil.copyfileobj(req.raw, file)
    await ctx.send(file = discord.File(data_name))
    os.remove(data_name)

#########################################################################################
#ASTRONOMICAL-STUFF
@client.command()
async def apod(ctx):

    url = f"https://api.nasa.gov/planetary/apod?api_key={NASE_API_KEY}"
    json_url = requests.get(url)
    json_data = json.loads(json_url.text)
    content_url = json_data["hdurl"]
    embed = Embed(title = f"{json_data['title']} {json_data['date']}", description = json_data['explanation'])
    embed.set_image(url = content_url)
    await ctx.send(embed = embed)

    #try:
    #    content_url = json_data["hdurl"]
    #    embed = Embed(title = f"{json_data["title"]json_data['title']} {json_data['date']}", description = json_data['explanation'])
    #    embed.set_image(content_url)
    #
    #    data_name = content_url.split('/')[-1]
    #    req = requests.get(content_url, stream = True)
    #    req.raw.decode_content = True
#
#        with open(data_name,'wb') as file: shutil.copyfileobj(req.raw, file)
#        await ctx.send(f"> Title: {json_data['title']}\n> Date: {json_data['date']}\n> Explanation: {json_data['explanation']}", file = discord.File(data_name))
#        os.remove(data_name)
#    except:
#        await ctx.send(f"> Title: {json_data['title']}\n> Date: {json_data['date']}\n> Explanation: {json_data['explanation']}\n{json_data['url']}")


@client.command()
async def mars(ctx, rover, camera, date):

    if rover not in ["Curiosity", "Opportunity", "Spirit"]:
        rover = choice(["Curiosity", "Opportunity", "Spirit"])
        await ctx.send(f"The rover you specified does not exist. I picked {rover} at random for you!")

    if camera not in ["FHAZ", "RHAZ", "NAVCAM"]:
        camera = choice(["FHAZ", "RHAZ", "NAVCAM"])
        await ctx.send(f"The rover you specified does not exist. I picked {camera} at random for you!")

    url = f"https://api.nasa.gov/mars-photos/api/v1/rovers/{rover}/photos?earth_date={date}&camera={camera}&api_key={NASE_API_KEY}"
    rover_cameras = {"FHAZ": "Front Hazard Avoidance Camera", "RHAZ": "Rear Hazard Avoidance Camera", "NAVCAM": "Navigation Camera"}
    year, month, day = date.split("-")
    try:
        json_url = requests.get(url)
        json_data = json.loads(json_url.text)
        content_url = json_data["photos"][0]["img_src"]
        data_name = content_url.split('/')[-1]
        req = requests.get(content_url, stream = True)
        req.raw.decode_content = True

        with open(data_name,'wb') as file: shutil.copyfileobj(req.raw, file)
        await ctx.send(f"> Rover: {rover}\n> Camera: {rover_cameras[camera]} ({camera})\n> Date: {day}day {month}month {year}year",file = discord.File(data_name))
        os.remove(data_name)

    except: await ctx.send("You propably entered the wrong date. I dont really know . . . just try again, you can do it! I believe in you! <3")

@client.command()
async def rovercams(ctx): await ctx.send("```FHAZ: Front Hazard Avoidance Camera\nRHAZ: Rear Hazard Avoidance Camera\nNAVCAM: Navigation Camera```")

#########################################################################################
#SMILEY STUFF
@client.command()
async def cheer(ctx):

    url = "https://www.affirmations.dev/"
    json_url = requests.get(url)
    json_data = json.loads(json_url.text)
    content_url = json_data["affirmation"]
    await ctx.send(content_url)

@client.command()
async def ping(ctx): await ctx.send(f"Pong! {client.latency * 1000}ms")

client.run(TOKEN)
