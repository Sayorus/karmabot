
**karmabot** is a Slack bot that listens for and performs karma operations.

*The main difference from other bots is that this one is community oriented.
This means that no karma will be applied unless community react using emoji.*

## Syntax

- Initiate a karma voting by posting to public channels:
  - `@karmabot @username ++ for blah blah`
  - `@karmabot @username -- for blah blah`

  Number of `+` or `-` is limited to `karma.max_diff` points (see the **Usage** section below).
  Upvote/downvote a user by adding reactjis to their message.

- Get/set a karma of specific user by posting a direct message to karmabot.

## Installation

### pipx

```sh
$ pipx install git+https://github.com/hacklabkyiv/karmabot
$ karmabot-init
$ # update config at ~/.config/karmabot/config.yml
$ karmabot
```

### Docker

Install `docker` and then:

```sh
$ git clone https://github.com/hacklabkyiv/karmabot && cd karmabot
$ # update config at ./data/config.yml
$ docker build -t karmabot .
$ docker run -d --name karmabot --restart=unless-stopped --network=host -it karmabot
```

This `Dockerfile` from repo contains setup for RaspberryPi.
You can modify **FROM** field in order to target your distro.

### Locally

```sh
$ git clone https://github.com/hacklabkyiv/karmabot && cd karmabot
$ make install
$ karmabot-init
$ # update config at ~/.config/karmabot/config.yml
$ karmabot
```

### systemd

```sh
$ pipx install git+https://github.com/hacklabkyiv/karmabot
$ karmabot-init
$ # update config at ~/.config/karmabot/config.yml
$ sudo wget https://raw.githubusercontent.com/hacklabkyiv/karmabot/refs/heads/main/systemd/karmabot.service -o /etc/systemd/system/karmabot.service
$ # update .service at /etc/systemd/system/karmabot.service by your environment
$ sudo systemctl start karmabot.service
$ sudo systemctl enable karmabot.service
```


## Usage

1. Add a [Slack Bot](https://api.slack.com/bot-users) integration
2. Invite `karmabot` to any existing channels and all future channels
3. Run `karmabot`

### 📆 Autoposting

Set a channel in `digest.channel` and a day of a month in `digest.day` and get a monthly digest.


### 📋 Configuration

| option                      | required? | description                              | default                          |
| --------------------------- | --------- | ---------------------------------------- | -------------------------------- |
| `log_level`                 | no        | set log level                            | `INFO`                           |
| `lang`                  | no        | options: en, uk                          | en                               |
| `slack_bot_token`           | **yes**   | slack bot token                          |                                  |
| `slack_app_token`           | **yes**   | slack SocketMode app token                          |                                  |
| `admins`                | no        | admins who can set karma to users        |                                  |
| `karma.initial_value`       | no        | the default amount of user karma         | `0`                              |
| `karma.max_diff`            | no        | the maximum amount of points that users can give/take at once | `5`         |
| `karma.vote_timeout`        | no        | a time to wait until a voting closes     | `true`                           |
| `karma.upvote_emoji`        | no        | reactjis to use for upvotes.             | `+1`, `thumbsup`, `thumbsup_all` |
| `karma.downvote_emoji`      | no        | reactjis to use for downvotes.           | `-1`, `thumbsdown`               |
| `karma.self_karma`          | no        | allow users to add/remove karma to themselves | `false`                     |
| `digest.channel`         | no        | channel to post digest to                |                                  |
| `digest.day`             | no        | a day when auto digest will be posted    | `1`                              |
| `db`                   | **yes**        | DB URI | `postgresql://admin:qwe123@localhost:5432/mydb`    |


### 📖 Commands

All the commands should be sent into direct messages to **karmabot**.

| command   | arguments                       | description                             |
| --------- | ------------------------------- | --------------------------------------- |
| get       | `@username`                     | get a user's karma                      |
| set       | `@username <points>`            | set a user's karma to a specific number |
| digest    |                                 | show users' karma in descending order (zero karma is skipped)|
| pending   |                                 | show pending votings                    |
| config    |                                 | show config for this execution          |
| help      |                                 | show this message                       |


## Translation

Multi-language depends on `msginit` and `msgfmt` which are parts of `gettext` package.

```sh
sudo apt-get install gettext
```

A language system consists of:

- a `.pot` file that is a template that defines all the available
for translation phrases
- a `.po` file for each language - a file with translations for a particular
language
- a `.mo` file - a binary representation of the `.po` file that is used in runtime

The application uses `.mo` files in runtime so it's critical to update them
upon editing `.po` files.


### Add a new language

```sh
msginit --no-translator -i lang/karmabot.pot -l uk_UA.UTF-8
```

### Update binaries (`*.mo` files) after modification of `*.po` files

```sh
msgfmt -o lang/<LANG>/LC_MESSAGES/messages.mo lang/<LANG>/LC_MESSAGES/messages.po
```

## Slack application configuration

- Basic Information
  - App-Level Tokens
    - Create an app token (`config.slack_app_token`)
- Socket Mode
  - Enable Socket Mode
  - Features affected
    - Interactivity and Shortcuts
      - Turn on
    - Slash Commands
      - Add `/karmabot`
    - Enable Events
      - Subscribe to bot events:
        - app_mention
        - message:channels
        - team_join

## License

see [./LICENSE](/LICENSE)
