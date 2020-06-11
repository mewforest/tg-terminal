# tg

Telegram terminal client.


## Features

- [X] view mediafiles: photo, video, voice/video notes, documents
- [X] ability to send pictures, documents, audio, video
- [X] reply, edit, forward, delete, send messages
- [X] stickers
- [X] notifications
- [X] record and send voice msgs
- [X] auto download files
- [X] toggle chats: pin/unpin, mark as read/unread, mute/unmute
- [X] Message history

TODO:

- [ ] secret chats
- [ ] list contacts
- [ ] search
- [ ] show users and their status in current chat
- [ ] create new chat
- [ ] bots (bot keyboard)


## Usage

From pip:

```sh
pip3 install tg
```

From sources:

```sh
git clone git@github.com:paul-nameless/tg.git
cd tg
PYTHONPATH=. python3 tg/main.py
```

Docker (voice recordings and notifications won't work):
```sh
docker run -it --rm tg
```

## Optional dependencies

- [tdlib](https://tdlib.github.io/td/build.html?language=Python) - in case of incompatibility with built in package
  For macOS:
  ```sh
  brew install tdlib
  ```
  and then set in config `TDLIB_PATH`
- `python3.8`
- `pip3 install python-telegram` - dependency for running from sources
- `terminal-notifier` or other program for notifications (see configuration)
- `ffmpeg` to record voice msgs and upload videos correctly


## Configuration

Config file should be stored at `~/.config/tg/conf.py`. This is simple python file.

### Simple config:

```python
PHONE = "[your phone number]"
```

### Advanced configuration:

```python
import os

# You can write anything you want here, file will be executed at start time
# You can keep you sensitive information in password managers or gpg
# encrypted files for example
def get_pass(key):
    # retrieves key from password store
    return os.popen("pass show {} | head -n 1".format(key)).read().strip()


PHONE = get_pass("i/telegram-phone")
# encrypt you local tdlib database with the key
ENC_KEY = get_pass("i/telegram-enc-key")

# log level for debugging
LOG_LEVEL = "DEBUG"
# path where logs will be stored (all.log and error.log)
LOG_PATH = "/var/log/tg/"

# If you have problems with tdlib shipped with the client, you can install and
# use your own
TDLIB_PATH = "/usr/local/Cellar/tdlib/1.6.0/lib/libtdjson.dylib"

# you can use any other notification cmd, it is simple python file which
# can format title, msg, subtitle and icon_path paramters
# In these exapmle, kitty terminal is used and when notification is pressed
# it will focus on the tab of running tg
NOTIFY_CMD = '/usr/local/bin/terminal-notifier -title "{title}" -subtitle "{subtitle}" -message "{msg}" -appIcon "{icon_path}" -sound default -execute \'/Applications/kitty.app/Contents/MacOS/kitty @ --to unix:/tmp/kitty focus-tab --no-response -m title:tg\''

# You can use your own voice recording cmd but it's better to use default one.
# The voice note must be encoded with the Opus codec, and stored inside an OGG
# container. Voice notes can have only a single audio channel.
VOICE_RECORD_CMD = "ffmpeg -f avfoundation -i ':0' -c:a libopus -b:a 32k '{file_path}'"

# You can customize chat and msg flags however you want.
# By default words will be used for readability, but you can make
# it as simple as one letter flags like in mutt or add emojies
CHAT_FLAGS = {
    "online": "●",
    "pinned": "P",
    "muted": "M",
    "unread": "U",
}
MSG_FLAGS = {
    "selected": "*",
    "forwarded": "F",
    "new": "N",
    "unseen": "U",
    "edited": "E",
    "pending": "...",
    "failed": "💩",
}
```


## Keybindings

vi like keybindings are used in the project. Can be used commands like `4j` - 4 lines down.

For navigation arrow keys also can be used.

### Chats:

- `j,k`: move up/down
- `J,K`: move 10 chats up/down
- `l`: open msgs of the chat
- `m`: mute/unmute current chat
- `p`: pin/unpin current chat
- `u`: mark read/unread
- `r`: read current chat

## Msgs:

- `j,k`: move up/down
- `J,K`: move 10 msgs up/down
- `G`: move to the last msg (at the bottom)
- `l`: if video, pics or audio then open app specified in mailcap file, for example:
  ```
  # Images
  image/png; icat %s && read
  audio/*; mpv %s
  ```
  if text, open in less (to view multiline msgs)
- `e`: edit current msg
- `<space>`: space can be used to select multiple msgs for deletion or forwarding
- `y`: yank (copy) selected msgs with <space> to internal buffer
- `p`: forward (paste) yanked (copied) msgs to current chat
- `dd`: delete msg for everybody (multiple messages will be deleted if selected)
- `i or a`: insert mode, type new message
- `I or A`: open vim to write long msg and send
- `v`: record and send voice message
- `r,R`: reply to a current msg
- `sv`: send video
- `sa`: send audio
- `sp`: send picture
- `sd`: send document
- `c`: copy current msg text or path to file if this is document, photo or video
- `]`: next chat
- `[`: prev chat
