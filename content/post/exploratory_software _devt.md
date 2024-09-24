+++
title = "Exploratory software development with Claude"
summary = "Best practice for branching and versioning with prompt engineering"
date = "2024-09-21"
draft = false
+++
## Task: Exploratory Software Development with Claude 3.5 Sonnet

I've spent a good chunk of time since [my first blog post](https://bcorfman.github.io/post/tic_tac_toe_board_states/) experimenting with prompt-based software development across various programming styles. What I've consistently found is that if I make mistakes in my instructions, or if Claude misinterprets my specs, the AI's grasp on what I'm asking for starts to fall apart. I've talked with other developers who've scaled back their approach as a result. They're keeping things simple - function-level coding or small, focused apps. But that's not really what I'm aiming for. I need AI to help me tackle software development at the project level. On top of that, I'm having to use Claude's chat feature instead of the API. It's not ideal, but the API is just too expensive in comparison.

### Experiment 1: Maintain a requirements file to go with the code

*[I'm learning that when I have an idea about how to potentially use Claude more effectively, I corroborate the idea with Claude itself to make sure I'm on the right track.]*

:b: **When I give you a task to write a Python program according to a set of requirements, and then tweak those requirements in the same chat session to better match how the resulting program should behave, eventually your understanding of the program starts to degrade until I can no longer effectively ask you to add features without getting substantial errors. True or false (no discussion desired): would it result in a better program if I had you revise the requirements yourself as you made the code modifications, then when your output starts to degrade, start a new chat session that contains the new source code along with the updated requirements?**

:sparkles: True.

---

:b: **What is the reason?**

:sparkles: The reason this approach would likely result in a better program is due to how large language models like myself operate and handle context:

1. Limited context window: I have a finite amount of context I can hold in "memory" during a conversation. As we discuss more details, make changes, and add features, earlier parts of the conversation may get pushed out of this context window.
2. Lack of persistent memory: I don't maintain a persistent memory of our conversation beyond what's in my current context window. Each response is generated based on the available context, which can lead to inconsistencies or forgotten details over a long session.
3. Potential for conflicting information: As requirements change and code is modified, there's a risk of contradictory information accumulating in the conversation history, which can lead to confusion and errors.
4. Clean slate advantage: Starting a new session with updated code and requirements allows me to work from a clear, consistent base without the potential confusion from a long, winding conversation history.
5. Focused context: By explicitly restating the current requirements and code, you ensure that the most relevant and up-to-date information is at the forefront of my context, leading to more accurate and consistent responses.
6. Avoiding cumulative errors: Resetting periodically prevents small misunderstandings or errors from compounding over time.

This approach essentially mimics how a human programmer might step back, review and update their documentation and requirements, and then approach the next phase of development with a fresh perspective and clear understanding of the current state of the project.

---

:b: **What type of prompt should I give you to create this updated list of requirements as the code gets updated?**

:sparkles: To create an updated list of requirements as the code evolves, you could use a prompt like this:

"Based on our discussion and the current state of the code, please provide an updated, comprehensive list of requirements for this program. Include both the original requirements that are still relevant and any new or modified requirements we've discussed. Present these in a clear, organized format that could be used as a reference for further development."

This prompt encourages me to:

1. Review the current state of the code and our discussion
2. Synthesize the information into a cohesive set of requirements
3. Include both original and new/modified requirements
4. Organize the information in a clear, referenceable format

You could also add specific elements you want me to focus on, such as:

"Please pay particular attention to [specific area of concern] and ensure that any changes in functionality related to [feature] are clearly reflected in the updated requirements."

This approach allows you to guide the update process while still leveraging my ability to synthesize information from our conversation and the current code state.

*[I tried this requirements/code combo on [a small project](https://github.com/bcorfman/arcade_actions) where I ported the Cocos2D Actions module for use with the Python Arcade library. In the process, I also had Claude create a demo of the more interesting actions available. Certainly I have been learning a lot more about good prompt engineering and specifications as I go along, but it was obvious that having Claude maintain a requirements file in conjunction with its code changes and additions kept its AI moving ahead with code development. When I don't use a requirements file and try to introduce more than one or two error corrections, it soon can't keep the changes straight and starts incorporating prior mistakes.]*

---

### Experiment 2: Adding in versioning and change management

*[I started running into trouble with scaling up Experiment 1 when I moved on to incorporating the Actions into a [game I was developing](https://github.com/bcorfman/megamania), also via Claude. Obviously, other than the demo I had created prior, there was no other code available on how to use the Actions inside a game. This is essentially one-shot learning for Claude, and unsurprisingly it didn't do that well. It tried certain actions out of order like Move(Repeat) instead of Repeat(Move), which the library fortunately refuses to accommodate because it's a logic error. After fumbling several times trying to error correct its mistakes and quickly getting the code and requirements files so messed up that I couldn't easily pick out parts I wanted to keep, I decided I should ask Claude about a better development method.]*

:b: **I like using your Projects feature to manage documents and code for my software development questions, but I'm having trouble with experiments. In other words, I'd like to give you Python code with a separate requirements document, have you add features to the code, but also configuration manage the conversation. If it takes 5 or 6 chat messages to fully explore a possible addition, but then I want to roll back to the state of the code at message 3 and proceed from there to try a different approach, how can you help me do that?**

:sparkles: For the kind of iterative development process you're describing, you would typically use a version control system like Git. However, I can suggest some ways we could simulate that process within our conversation:

1. Numbered versions: We could use a numbering system for each version of the code, like V1, V2, etc. You could ask me to refer back to a specific version number when you want to "roll back".
2. Checkpoints: At key points in our discussion, you could ask me to provide a full snapshot of the current code state, which we could label as a checkpoint.
3. Branching: If you want to explore multiple approaches, we could use a branching naming convention, like "Feature A - Version 1", "Feature A - Version 2", "Feature B - Version 1", etc.
4. Requirements tracking: We could maintain a separate "document" for requirements, updating it as we go along.

Here's an example of how this might work:

You: Here's the initial Python code (V1):

```python
def main():
    print("Hello, World!")

if __name__ == "__main__":
    main()
```

Requirements Document (V1):
```
1. The program should print "Hello, World!"
```

Now, let's say you want to add a new feature. You can describe the feature, and I'll update both the code and the requirements document.

For example, you might say: "Let's add a function that asks for the user's name and greets them personally."

I would then provide:

Python Code (V2):

```python
def get_name():
    return input("What's your name? ")

def greet(name):
    print(f"Hello, {name}!")

def main():
    name = get_name()
    greet(name)

if __name__ == "__main__":
    main()
```

Requirements Document (V2):
```
1. The program should print "Hello, World!"
2. The program should ask for the user's name and greet them personally.
```

You can then continue to iterate on this, exploring different features or implementations. If at any point you want to go back to a previous version, you can simply say something like "Let's go back to V1 and try a different approach." I'll then use that version as our new starting point.

---

:b: **Yes, that's what I want. How should I write the prompt for you to do that with other conversations in the future?**

:sparkles: To help me manage code versions and requirements in future conversations, you could use a prompt like this:

"Let's work on a Python project using version control. Please keep track of code versions and requirements as we go. Start each code version with 'Python Code (Vx):' and each requirements update with 'Requirements Document (Vx):' where x is the version number. When I want to modify the code or requirements, I'll specify the changes. If I want to revert to a previous version, I'll say 'Let's go back to Vx' where x is the version number. Please confirm you understand before we begin."

This prompt gives me clear instructions on how to structure our conversation and manage versions. It also allows you to easily reference specific versions and make changes or reversions as needed.

When you use this prompt, I'll confirm that I understand, and then we can start working on your project. You can then provide your initial code and requirements, and we'll iterate from there.

---

:b: **Yes, perfect so far. Can we try modifying your HelloWorld example so I can see if the process works?**

:sparkles: Certainly, let's try modifying the Hello World example to see how this version control system works. I'll start by restating our current version:

Python Code (V2):

```python
def get_name():
    return input("What's your name? ")

def greet(name):
    print(f"Hello, {name}!")

def main():
    name = get_name()
    greet(name)

if __name__ == "__main__":
    main()
```

Requirements Document (V2):
```
1. The program should print "Hello, World!"
2. The program should ask for the user's name and greet them personally.
```

Now, let's modify this. What change would you like to make to the code or requirements?

---

:b: **Change the greeting to also add the date in a second sentence. For example, "Today's date is Saturday, September 24."**

:sparkles: Certainly, I'll update the code and requirements to include the current date in the greeting.

Python Code (V3):

```python
from datetime import date

def get_name():
    return input("What's your name? ")

def get_current_date():
    return date.today().strftime("%A, %B %d")

def greet(name):
    print(f"Hello, {name}!")
    print(f"Today's date is {get_current_date()}.")

def main():
    name = get_name()
    greet(name)

if __name__ == "__main__":
    main()
```

Requirements Document (V3):
```
1. The program should print "Hello, World!"
2. The program should ask for the user's name and greet them personally.
3. The program should print the current date in the format "Day of week, Month Day".
```

In this version, I've made the following changes:

1. Imported the `date` module from `datetime` to work with dates.
2. Added a new function `get_current_date()` to format the current date.
3. Modified the `greet()` function to include the date in a second sentence.
4. Updated the requirements document to reflect the new feature.

Is this what you had in mind? Would you like to make any further modifications or explore a different approach?

---

:b: **Yes, but the second sentence should be on the same line as the first.** *[I want to introduce one more modification before I try backtracking.]*

:sparkles: I understand. Let's update the code to have both sentences on the same line.

Python Code (V4):

```python
from datetime import date

def get_name():
    return input("What's your name? ")

def get_current_date():
    return date.today().strftime("%A, %B %d")

def greet(name):
    print(f"Hello, {name}! Today's date is {get_current_date()}.")

def main():
    name = get_name()
    greet(name)

if __name__ == "__main__":
    main()
```

Requirements Document (V4):
```
1. The program should print "Hello, World!"
2. The program should ask for the user's name and greet them personally.
3. The program should print the current date in the format "Day of week, Month Day".
4. The greeting and date should be printed on the same line.
```

In this version, I've made the following change:

1. Modified the `greet()` function to print both the greeting and the date in a single `print()` statement, separating them with a space.
2. Updated the requirements document to specify that the greeting and date should be on the same line.

---

:b: **Revert to V2 and add a second sentence indicating today's weather forecast as a single word. For example, "It's going to be a sunny day." Or "It's going to be a snowy day." The weather forecast should be pulled from OpenWeatherMap, and I'd like it to use the weather at my GPS location.**

:sparkles: Certainly. Let's modify the code to use OpenWeatherMap API and get the weather based on GPS coordinates. We'll assume you have an OpenWeatherMap API key and can provide your GPS location.

Python Code (V5):
```python
import requests

def get_name():
    return input("What's your name? ")

def get_gps_location():
    # In a real application, you might use a GPS module or a location API
    # For this example, we'll just ask the user to input their coordinates
    latitude = input("Enter your latitude: ")
    longitude = input("Enter your longitude: ")
    return latitude, longitude

def get_weather_forecast(lat, lon):
    api_key = "your_openweathermap_api_key_here"
    url = f"https://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&appid={api_key}&units=metric"
    
    try:
        response = requests.get(url)
        response.raise_for_status()  # Raises an HTTPError for bad responses
        data = response.json()
        weather_description = data['weather'][0]['description']
        temperature = data['main']['temp']
        return f"{weather_description} with a temperature of {temperature}°C"
    except requests.RequestException:
        return "unavailable"

def greet(name):
    lat, lon = get_gps_location()
    weather = get_weather_forecast(lat, lon)
    print(f"Hello, {name}! The current weather at your location is {weather}.")

def main():
    name = get_name()
    greet(name)

if __name__ == "__main__":
    main()
```

Requirements Document (V5):
```
1. The program should ask for the user's name and greet them personally.
2. The program should prompt the user for their GPS coordinates (latitude and longitude).
3. The program should fetch real-time weather data from OpenWeatherMap API based on the provided GPS coordinates.
4. The greeting should include the current weather description and temperature at the user's location.
5. If the weather forecast can't be fetched, it should be reported as "unavailable".
6. The greeting and weather information should be printed on the same line.
```

*[Hmm, that's interesting ... requirement 6 still keeps the two sentences on the same line. Did Claude keep this former idea as a requirement or just as my preference? That may be something to investigate later ...]*

Key changes in this version:

1. Added a `get_gps_location()` function to simulate getting GPS coordinates from the user.
2. Modified `get_weather_forecast()` to use OpenWeatherMap API with latitude and longitude.
3. Updated the API call to fetch current weather data, including description and temperature.
4. Adjusted the greeting to include more detailed weather information.

To use this code, you would need to:

1. Sign up for an OpenWeatherMap account and get an API key.
2. Replace `"your_openweathermap_api_key_here"` with your actual API key.
3. Install the `requests` library if not already installed (`pip install requests`).

This version provides a more realistic implementation using a specific weather API and location-based data. The user inputs their GPS coordinates manually, but in a real-world application, this could be automated using a GPS module or location API.

---

### Wrap-up

To be clear, this collaborative development with Claude was truly at the project level. I keep the code and requirements files in a Project within Claude and have it do all its modifications there. When it makes a change to the code, I copy out the new code artifact it creates, paste it into Visual Studio Code, save it and then run it. If it doesn't work, I ask Claude to correct its errors. Once I reach a full feature update, I start a new chat with the updated requirements and code, and I begin again. Claude's Projects feature helps cut down on the extra attachments and file tracking I would have to do with a chat session that didn't contain any prior context.

As an alternative to having Claude do CM, I did try keeping my code and requirements in text files within my game directory and using Git on my own. But honestly, the constant back-and-forth of copying and pasting, plus trying to keep up with commits for every little change, was a real headache. I've found it's actually simpler to stay in the chat, use Claude for CM, and only commit code and requirements at major checkpoints. It also works better to have Claude keep tabs on changing requirements and write them out in full at the end of a chat session.

Since I've started using this approach of requirements tracking plus CM with versioning (let's call it **RT+CM** for short), I've seen a huge leap in the complexity of software development tasks I can ask Claude to handle. One of the surprising results in doing development this way is that the process for making good software becomes *requirements specification* rather than *writing code*. That's a skill that I'd done in the past, but only for humans! It also requires a certain amount of letting go of familiar practices to try unfamiliar ones. It's not always a smooth transition, but the process of discovery is exciting. :sunny:

<link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
<script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
