import discord
import asyncio
import random
import requests
import io
import safygiphy

client = discord.Client()
g = safygiphy.Giphy()

gruppen = None
gruppenuser = None

minutes = 0
hour = 0

players = {}


@client.event
async def on_ready():
    print("Eingeloggt als:")
    print(client.user.name)
    print(client.user.id)
    await client.change_presence(game=discord.Game(name=".duck help -> im Teisch", type=1))


@client.event
async def on_member_join(member):
    server = member.server
    role = discord.utils.find(lambda r: r.name == "Duck-Member", server.roles)
    await client.add_roles(member, role)

@client.event
async def on_message(message):
    if message.content.startswith('.duck gif'):
        gif_tag = "rubber duck"
        rgif = g.random(tag=str(gif_tag))
        response = requests.get(
            str(rgif.get("data", {}).get('image_original_url')), stream=True
        )
        await client.send_file(message.channel, io.BytesIO(response.raw.read()), filename='randomgif.gif')

    if message.content.lower().startswith(".duck say"):
        duck = message.content[11:28]
        await client.send_message(message.channel,                                  "```      _\n  .__(.)< (" + duck + ")\n   \___)\n~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~```")

    if message.content.lower().startswith('.duck flip'):  # Coinflip 50/50% chance kopf oder zahl
        choice = random.randint(1, 2)
        if choice == 1:
            await client.send_message(message.channel, "🐣 ▶ 🐥")
        if choice == 2:
            await client.send_message(message.channel, "🐣 ◀ 🐥")

    if message.content.startswith('.duck uptime'):
        await client.send_message(message.channel,
                                  "`Ich bin schon {0} stunde/n und {1} minuten online auf {2}. `".format(hour, minutes,
                                                                                                         message.server))

    if message.content.lower().startswith('.duck help'):
        await client.delete_message(message)
        embed = discord.Embed(
            title="---------------------------------------------------------------------\n",
            color=0xe67e22,
            description="\n"
        )
        embed.set_author(
            name="I'm Duck-The-Bot hir you see my commands.",
            icon_url="http://fs5.directupload.net/images/170927/wafmedz8.png",
        )
        embed.add_field(
            name=":pencil:Commands",
            value="-------------------------------------------------------------------\n"
                  ".duck help - Show you all the commands.\n"
                  ".duck gif - Sends a random duckgif into the channel.\n"
                  ".duck flip - Throws a duck for you.\n"
                  ".duck say <text> - Let the duck say your text.\n"
                  ".duck uptime - Shows how long the bot is online.\n"
                  ".duck invite - Hir you get the link to invite the bot.\n",

            inline=False
        )
        embed.set_footer(
            text="Bot made by Mein-Code.",
            icon_url="http://fs5.directupload.net/images/170927/wafmedz8.png",
        )

        await client.send_message(message.channel, embed=embed)

    if message.content.lower().startswith('.duck invite'):
        embed = discord.Embed(
            title="---------------------------------------------------------------------\n",
            color=0xe67e22,
            description="\n"
        )
        embed.set_author(
            name="Ich bins Duck-The-Bot hir kannst du mich auf deinen Discord einladen.",
            icon_url="http://fs5.directupload.net/images/170927/wafmedz8.png",
        )
        embed.add_field(
            name="https://discordapp.com/oauth2/authorize?client_id=362250787642015744&scope=bot&permissions=104332487\n",
            value="-------------------------------------------------------------------\n",

            inline=False
        )
        embed.set_footer(
            text="Bot made by Mein-Code.",
            icon_url="http://fs5.directupload.net/images/170927/wafmedz8.png",
        )

        await client.send_message(message.channel, embed=embed)


async def tutorial_uptime():
    await client.wait_until_ready()
    global minutes
    minutes = 0
    global hour
    hour = 0
    while not client.is_closed:
        await asyncio.sleep(60)
        minutes += 1
        if minutes == 60:
            minutes = 0
            hour += 1


client.loop.create_task(tutorial_uptime())

client.run('Your Bot Token')
