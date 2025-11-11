## Features
The first thing I'm gonna do is map out core features I want my app to have. They are:
- Managing viewbots on stream (count and actions);
- Viewbot viewing stream;
- Viewbot messaging in chat, preferably with context so it feels at least half real;
- Viewbot polls interaction;
After figuring this out I started researching deeper.
## Research
### Kick defense mechanism
Since messaging on kick is allowed only for authorized users, I also will need:
- Bots registration and authorization
And this is where things get tricky. At first I was surprised there is no reCaptcha on the platform, but later I found out that kick uses Kassada defense system. I found a beautiful [paper](https://ru.scribd.com/document/899949744/Understanding-Kasada-Bot-Defense-and-Bypass-Mechanisms) on how it works.
I also found some solutions to the account generation problem like [this](https://github.com/fqw3/Kick.com-account-generator), and general [kick bots projects](https://github.com/topics/kick-bot)
The thing is there clearly are tools to bypass Kassada (there are a lot of on the internet), but all the secrets are kept behind payed APIs. I would like to earn on this too :D
Okay, we'll try it our way.
My solution to it is to use headless browser with `head on` option. This way I can bypass algorithms that depends on WebGL execution, all proof of work algorithms will be executed in runtime and keep automation. Also this way I don't need to tweak TLS a lot (at least I think I don't). Also I might need some kind of user mouse movement simulation, but that is details. If this approach doesn't work I can try automating actual browser actions, which won't be perfect.
To further obfuscate my connection I will use Tor network. If it is allowed then the end server will have no way of figuring out end IP which reduces possibility of getting banned to zero. Also this may solve the problem with request rate limiting, though I'm not sure if it exists on the platform.
Another thing I will need for account creation is SMTP server. There I'll get emails with verification codes that will be passed to the bots during verification.
I don't need to emulate any other process besides login, since all the API calls on kick backend need nothing but JWT token. So I'll save access and refresh tokens somewhere and later will use them as needed.
### Chatting
Viewing and polling is kinda easy (it is a simple API calls), but chatting is a bit more complicated. Let's say I want context to be used. Then I probably need some kind of LLM that will generate text based on the ruleset. Also I will need a context, which may be:
- Stream image itself
- Chat history
- Streamer's speech-to-text and emotions
I decided to leave stream image for later since I don't think I can get a lot of chat interaction from it and it may load my not so powerful system a lot.
There are a lot of lightweight LLM solutions so at least that part will not be a problem. I will run one in Ollama docker container. Also it will be easy to switch to other one deployed in a cloud if I'd need more chatters horsepowers.
There are also a lot of SSP solutions, and streaming voice into one of it is not a big deal.
My only concern here is whether the end device have enough CPU and GPU power, especially if there is a game launched in the background. It may turn out to be costly.
## System design
Lets remember all the things Uncle Bob teached us and try to design at least half decent system :D 
Clearly I see a few parts of the system:
### Accounts creation
This is a big deal because if I won't find a way to bypass defense system a lot of other features will not be possible to implement. And I believe there are a lot of brilliant minds working on Kassada that will be hard to compete with.
There we have a few systems:
1. Browser automation handler
2. User actions mimicking
3. Tor requests traffic obfuscation
4. Email service handler
