# Obsidian-JG-Method
A starter kit that follows how I use Obsidian to manage my goals, tasks, notes, and software development knowledge base.

# Starter Vault
In order to start using it, I suggest copying and moving it to wherever you want your notes to live. I personally have my vault inside of a folder called "Obsidian" inside of my iCloud Drive folder.

# Alfred Workflow
This assumes that you have You will likey need to edit the path to your vault in order to get this to work. In order to do this, you will need to open the Alfred workflow (in the Alfred Expansions and Workflow folder) and then edit the included "Run Script" action.

You will need to edit this line:

``` terminal
vaultPath = os.path.expanduser("~") + "/Library/Mobile Documents/com~apple~CloudDocs/Obsidian/Starter Vault"
```
# Alfred Text Expansions
In order to get access to this feature, you will need to buy the [Alfred Powerpack](https://www.alfredapp.com/powerpack/).

If you aren't using Alfred, here's a picture of the majority of my text expansions:

![Text Expansions](/alfred-text-expansions-pic.png)

Here are the final two:

| Name      | Keyword | Snippet |
| ----------- | ----------- | ----------- |
| Base Recurring Intention or Misc Task      | -base       | ♦︎       |
| Changed Plans   | -change        | 🚨        |
