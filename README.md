# Obsidian-JG-Method
A starter kit that follows how I use Obsidian to manage my goals, tasks, notes, and software development knowledge base.

# Starter Vault
In order to start using it, I suggest copying and moving it to wherever you want your notes to live. I personally have my vault inside of a folder called "Obsidian" inside of my iCloud Drive folder.

# Alfred Workflow
You will likey need to edit the path to your vault in order to get this to work. In order to do this, you will need to open the Alfred workflow (in the Alfred Expansions and Workflow folder) and then edit the included "Run Script" action.

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

# Here's the Python script that the Alfred workflow uses:

``` py
# Author: Joshwin Greene
import sys, os
from datetime import datetime, timedelta
from collections import defaultdict

def getFullPathToFile(file):
	return vaultPath + '/' + file

def isWithinThreeDays(file):
	fullPath = getFullPathToFile(file)
	if os.path.isfile(fullPath):
		modifiedTime = os.path.getmtime(fullPath)
		modifiedDateTime = datetime.fromtimestamp(modifiedTime)
	
		currentDateTime = datetime.now()

		threeDaysAgo = currentDateTime - timedelta(days=3)

		return modifiedDateTime > threeDaysAgo
	else:
		return False 

def fileHasNewKnowledge(file):
	fullPath = getFullPathToFile(file)
	result = False
	with open(fullPath, 'r') as reader:
		result = "Ready to be Recorded" in reader.read()
	return result

def updateOriginalFile(path):
	text = None	

	with open(path, 'r') as reader:
		text = reader.read()
		text = text.replace("Ready to be Recorded", "**Alfred:** Header Notes were Recorded on " + datetime.now().strftime('%Y-%m-%d %H:%M:%S'))
	
	with open(path, 'wt') as a_writer:
		a_writer.write(text)

# Enter the path to your vault
vaultPath = os.path.expanduser("~") + "/Library/Mobile Documents/com~apple~CloudDocs/Obsidian/Starter Vault"

# Get all of the files that were edited in the past 3 days

files = os.listdir(vaultPath)

result = filter(isWithinThreeDays, files)
	
# From all of these files, only use the ones that have the "Ready to be Recorded" text

newResult = filter(fileHasNewKnowledge, result)

# For each file...

# Get the file path and then use the split method with four hashtags as the separator

for file in newResult:
	filePath = getFullPathToFile(file)
	parts = []
	headerTitles = []
	destinations = []
	desiredDestinations = []

	with open(filePath, 'r') as reader:
		parts = reader.read().split('####')

	for i in range(1, len(parts) - 1):
		stripped = parts[i].strip()

		# Get the text before the new line
		newLineIndex = stripped.find('\n')
		
		parts[i] = stripped[:newLineIndex]

	
	# Ignore the first element and then get the title of each of the header notes (not using the last one)

	for j in range(1, len(parts) - 1):
		headerTitles.append(parts[j])

	# Get the list of destinations from the last header note and remove the closing brackets from each one
	destinations = parts[len(parts)-1].split('- [[')

	for k in range(1, len(destinations)):
		desiredDestinations.append(destinations[k].strip().replace("]]", ""))

	endOfListIndex = len(desiredDestinations)-1

	desiredDestinations[endOfListIndex] = desiredDestinations[endOfListIndex].replace("\n\nReady to be Recorded", "")

	# Create a dictionary where each key is each destination and their values are a list of header titles

	destinationDict = defaultdict(list)

	for h in range(len(headerTitles)):
		destinationDict[desiredDestinations[h]].append(headerTitles[h])

	# Append to all of the associated destinations in the correct format with a new line above each

	for destination, headerTitles in destinationDict.items():
		with open(getFullPathToFile(destination + '.md'), 'a') as a_writer:
			for title in headerTitles: 
    				a_writer.write('\n\n' + '![[' + file.replace('.md', '') + '#' + title + ']]')
		

	# Once all of the destination files are updated, edit the original file so that the "Ready to be Recorded" string is replaced by something more appropriate

	updateOriginalFile(filePath)

# Notify the user about how many files with header notes were processed
print("Number of Processed Files: " + str(len(newResult)))
```
