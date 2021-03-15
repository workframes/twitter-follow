# twitter-follow
**WARNING**: Mind this is against the twitter terms of service(https://help.twitter.com/en/using-twitter/twitter-follow-limit)

<img width="659" alt="Screen Shot 2021-03-15 at 9 12 30 AM" src="https://user-images.githubusercontent.com/68570351/111184823-93f2ed00-856e-11eb-82f4-f71c17ee67cf.png">

**Requirements**: Tweepy, Access to Twitters Api


**Main File(bot.py)**
```py
# import
from tweepy import OAuthHandler
from tweepy import Stream
from tweepy import API
from tweepy import StreamListener
from tweepy import RateLimitError
from tweepy import TweepError
from time import sleep
from credentials import credentials


global sleeping, api



# handle error
def error_handling(e):
    error = type(e)
    if error == RateLimitError:
        print("You've hit a limit! Sleeping for 30 minutes.")
        sleep(60 * 30)
    if error == TweepError:
        print('Uh oh. Could not complete task. Sleeping 10 seconds.')
        sleep(10)

# stream
class my_stream(StreamListener):
    def on_status(self, status):
        global api
        try:

            api.create_friendship(status.user.screen_name)
            print(f"New Friendship Created: https://twitter.com/{status.user.screen_name}")
            sleep(25)
            api.destroy_friendship(status.user.screen_name)
            print(f"Friendship Destroyed: https://twitter.com/{status.user.screen_name}")
            sleep(25)
        except (RateLimitError, TweepError) as e:
            error_handling(e)

    def on_error(self, status_code):
        if (status_code == 429):
            print("Limit probably reached")
        elif status_code == 420:
            # returning False in on_error disconnects the stream
            return False
        else:
            print("Error occured > {}".format(status_code))
        return False


def create_stream(_api):
    new_stream_listner = my_stream()
    new_stream = Stream(auth=_api.auth, listener=new_stream_listner)
    new_stream.filter(track=["#Roblox", "#GenshinImpact", "#Genshin_Impact"])

# login/create stream
auth = OAuthHandler(credentials['consumer_key'], credentials['consumer_secret'])
auth.set_access_token(credentials['access_token'], credentials['access_token_secret'])
api = API(auth, wait_on_rate_limit=True)
create_stream(api)
```

**credentials(credentials.py)**
```py
credentials = dict(
    screen_name = 'YOUR_TAG',
    consumer_key = 'xxx', 
    consumer_secret = 'xxx',
    access_token = 'xxx',
    access_token_secret = 'xxx',
)
```







