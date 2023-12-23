# Actionslaw

Automation workflows for GitHub Actions, but soft and tasty.

## 🏁 Getting Started

Build an Actionsflow workflow is a four-step process:

1. **Create a public Github repository [here](https://github.com/actionslaw/actionslaw/generate).**

   A typical Actionslaw repository structure looks like this:

   ```sh
   ├── .github
   │   └── workflows
   │       └── rss.yml
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

1. **Define your [workflow file](https://actionsflow.github.io/docs/workflow/) at `workflows` directory**

   A typical workflow file `rss.yml` looks like this:

   ```yaml
   name: Actionslaw
    on:
      schedule:
        - cron: "*/15 * * * *"
     push:
       branches:
         - main
   
   jobs:
     actionslaw:
       runs-on: ubuntu-latest
       outputs:
         items: ${{ steps.trigger.outputs.items }}
       steps:
         - uses: actionslaw/actionslaw-action@v0.9
           id: trigger
           with:
             on: '
               {
                 "rss": {
                   "url": "https://hnrss.org/newest?points=300&count=3"
                 }
               }'
   ```
   
1. **Commit and push your updates to Github**

Then, Actionslaw will run your workflows as you defined, you can view logs at your repository actions tab on [Github](https://github.com).
