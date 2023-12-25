# Socialslaw

Server-less automation workflows for GitHub Actions, but soft and tasty.

## 🏁 Getting Started

Build an Socialslaw is a four-step process:

1. **Create a public Github repository [here](https://github.com/actionslaw/socialslaw/generate).**

   A typical Socialslaw repository structure looks like this:

   ```sh
   ├── .github
   │   └── workflows
   │       └── socials.yml
   ├── .gitignore
   ├── README.md
   └── package.json
   ```

1. **Uncomment scheduled event**

    ```yml
    on:
      schedule:
        - cron: "*/15 * * * *"
    ```
    > Note: To prevent abuse, by default, the schedule is commented, please modify the schedule time according to your own needs, the default is once every 15 minutes. Learn more about schedule event, please see [here](https://docs.github.com/en/actions/reference/events-that-trigger-workflows#schedule)

1. **Uncomment the jobs for your socials**

   Configure the social media account that you'll use as the source of your posts by uncommenting them in `socials.yml`. For example, we've enabled the send tweet workflow here:

   ```yaml
     twitter:
       needs: actionslaw
       if: ${{needs.actionslaw.outputs.items != '[]' && needs.actionslaw.outputs.items != ''}}
       strategy:
         matrix:
           items: ${{fromJSON(needs.actionslaw.outputs.items)}}
       runs-on: ubuntu-latest
       steps:
         - uses: actions/cache/restore@v3
           with:
             path: ./tweets.txt
             key: ${{ matrix.items.replyto }}
   
         - id: read-history
           run: |
             if test -f ./tweets.txt; then
               cat ./tweets.txt  >> $GITHUB_OUTPUT
             fi
   
         - uses: rg-wood/send-tweet-action@v1
           id: send
           with:
             status: ${{ matrix.items.message }}
             replyto: ${{ steps.read-history.outputs.status }}
             consumer-key: ${{ secrets.TWITTER_CONSUMER_API_KEY }}
             consumer-secret: ${{ secrets.TWITTER_CONSUMER_API_SECRET }}
             access-token: ${{ secrets.TWITTER_ACCESS_TOKEN }}
             access-token-secret: ${{ secrets.TWITTER_ACCESS_TOKEN_SECRET }}
   
         - id: write-history
           run: echo 'status=${{ steps.send.outputs.status }}' >> ./tweets.txt
   
         - uses: actions/cache/save@v3
           with:
             path: ./tweets.txt
             key: ${{ matrix.items.uri }}
   ```

4. **Configure you credentials**

Setup the credential secrets for you social media accounts:

##### 🐦 Twitter

You need to set-up consumer and access tokens as GitHub Action secrets in your workflow project. See the [send-tweet-action documentation](https://github.com/marketplace/actions/send-and-reply-tweet-action#secret-configuration) for full instructions.

##### ☁️ BlueSky

You need to configure the authority and login credentials for your Bluesky account as GitHub Action. See the [send-bluesky-post documentation](https://github.com/marketplace/actions/send-and-reply-bluesky-action#specify-authority) for full instructions.


4. **Commit and push your updates to Github**

Then, Socialslaw will run your workflows as you defined, you can view logs at your repository actions tab at [Github](https://github.com)

## Run Locally

You can run self-hosted Socialslaw manuall.

## Prerequisites

1. Install [docker](https://docs.docker.com/get-docker/)
1. Install [act](https://github.com/nektos/act)
1. Install dependencies by running `npm install`

### Start

Configure your social account credentisl in a `.secrets` file at the root of this project. Start Socialslaw locally:

```bash
npm start
```
