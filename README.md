# Programming Assignment 7: Chatbot! 

**Late days CANNOT be used on this assignment. Please submit early and often to avoid last minute submission issues!**

**You must work in groups of 3-4 members (To work in a group of size 2, you must get special permission from the staff). All submissions will be graded according to the same criteria, regardless of group size.**

In this assignment you will build a chatbot somewhat like the chatbot ELIZA that you saw in the lecture. You will work on a simpler, single-agent version first, and then choose from a set of advanced features to get more points. **A score of 97 points is considered full credit (95 from chatbot functionality + 2 from ethics questions), and you can get up to 10 additional points in extra credit.**

To get you started, we will guide you in the implementation of a chatbot that can recommend movies to the user – this will be referred to as the **starter** mode. After you have implemented the movie-recommender chatbot, you will switch to a **creative** mode and implement an interesting extension of the movie recommender, or something totally new. Be creative, and have fun!

## Contents of this file!
This is a big file! Here are the main sections:

**Background and Overview:**
- [Background](#a-little-bit-of-background)
- [High Level Spec](#high-level-specification)

**Getting started: Starter Mode, REPL, and resources**
- [Starting to code: warm up](#starting-to-code-warm-up)
- [Starter code and REPL](#running-the-starter-code)
- [Starter Mode Overview](#starter-mode-60-points)
- [Writing code for starter mode](#writing-code-for-starter-mode)
- [Movie Database](#your-movie-database-movielens)
- [Sentiment Lexicon](#your-sentiment-lexicon)

**Debugging and Testing**
- [Running sanity check](#sanity-check)
- [Local script testing](#local-script-testing)
- [Printing debug info](#printing-debug-info) We give you some built in debugging tools! Check them out!
- [Important tips and notes](#important-notes-and-tips) **Important!!**
- [Other resources and code size](#other-resources-and-code-size)

**Creative Mode**
- [Creative Mode Overview](#creative-mode-45-points)
- [Switching to Creative Mode](#switching-to-creative-mode)
- [How are starter and creative mode different?](#difference-between-starter-mode-and-creative-mode)

**Evaluation and Rubric (answers to your grading questions!)**
- [Evaluation](#evaluation) (the rubric is here!)
- [Ethics question](#ethics-question) We're evaluating this, too!

**Submitting**
- [Submitting Your Solution](#submitting-your-solution) As always, submit **early AND often!!**

## A little bit of background
In the early 1960s, the well-known early artificial intelligence (AI) program ELIZA made a big impact after showing its ability to process input and produce output expressed in natural language. [ELIZA](https://en.wikipedia.org/wiki/ELIZA) was one of the first conversational agents in AI, designed to simulate intelligent conversations, also known as chatbots. Developed by [Joseph Weizenbaum](https://en.wikipedia.org/wiki/Joseph_Weizenbaum) at MIT, ELIZA had several modules to interact in English, German, and Welsh, and different modes of operation. The most famous was a module that simulated the interaction with a [Rogerian psychotherapist](https://en.wikipedia.org/wiki/Person-centered_therapy). A typical conversation with ELIZA (all caps) was as follows:

    You are like my father in some ways.
    WHAT RESEMBLANCE DO YOU SEE
    You are not very aggressive but I think you don't want me to notice that.
    WHAT MAKES YOU THINK I AM NOT AGGRESSIVE
    You don't argue with me.
    WHY DO YOU THINK I DON'T ARGUE WITH YOU
    You are afraid of me.
    DOES IT PLEASE YOU TO BELIEVE I AM AFRAID OF YOU`

After the initial success by ELIZA, a number of other chatbots were created with increased complexity – as an example, on the late 70s a chatbot named [PARRY](https://en.wikipedia.org/wiki/PARRY), created by Stanford’s Psychiatry professor [Kenneth Colby](https://en.wikipedia.org/wiki/Kenneth_Colby), simulated a psychotic patient that used a belief system internally, allowing for more complex behavior.

You will implement a chatbot similar to ELIZA, but even more sophisticated! This chatbot will recommend movies to the user. Let’s get started!

## High-level specification
Conversational agents like ELIZA can be decomposed into four modules (see Jurafsky and Martin 2008, [Norvig 1991](https://github.com/norvig/paip-lisp)), each one responsible for a specific task:

1. Read the input.
2. Extract relevant information from the input, which can be domain specific – as in our movie recommender chatbot.
3. Transform the input into a response – users normally expect a response in the given domain.
4. Print the response.

The starter code provided for the assignment handles modules (1) and (4) for you. You will be responsible for implementing modules (2) and (3), in both the starter and creative modes.

Once you have set up the environment, the warm-up section will help you familiarize yourself with the REPL. After you feel comfortable interacting with the REPL, you will implement the extraction and transformation modules for the movie recommender chatbot.

## Starting to code: warm up
Modules (1) and (4) are provided to you in this assignment using a common pattern in software engineering known as the Read-Eval-Print-Loop, REPL. The REPL creates a prompt that gets an input string from the user (module 1). Then the REPL passes the input to a chatbot class that is responsible for doing the work in modules (2) and (3). The response generated by the chatbot class is then handled again by the REPL, which prints the response and waits for more input. Let’s see how it works!

## Running the starter code
In the starter code folder, fire up the REPL by issuing the following command:
`python repl.py`

## Interacting with the REPL
You can type your message in the prompt to moviebot, our default chatbot, and hit enter. To exit the REPL, write `:quit` (or press Ctrl-C to force the exit). Right now the chatbot is not doing anything more than printing the string you entered – concatenated to a string that says it processed it.

## Interacting with REPL via testing scripts
In the testing/test_scripts/ folder, you will find some testing scripts. To run a script, enter the following command (replace testing/test_scripts/simple.txt with your desired script):

    python repl.py < testing/test_scripts/simple.txt
    
As you can see, each line of `simple.txt` is entered consecutively into the chatbot. However, the script terminates once it hits `:quit` and any lines afterwards will not be executed.

This will useful when you want to test the same script multiple times. We will be testing your code with similar (but not the same) scripts as the ones provided in test_inputs.

## Customizing your chatbot
All the code that you will need to write for this assignment will be in `chatbot.py`. Open that file and follow the instructions there to change the name of the chatbot, the greeting message, and the goodbye message. You can change your chatbot’s name now!

# Starter Mode (60 points)
In starter mode, your chatbot will help the user by giving movie recommendations. It will ask the user to say something about movies they have liked, and it will come up with a recommendation based on those data points. The user of the chatbot in starter mode will follow its instructions, so you will deal with fairly restricted input. Specifically, you can assume:
- **Movie names** will come in **quotation marks**
- **Expressions of sentiment** will be **simple**

Here is an example of how this might go:

    moviebot> Hi! I'm MovieBot! I'm going to recommend a movie to you. First I will ask you about your taste in movies. Tell me about a movie that you have seen.
    > I really liked Moneyball.

The bot gets confused because it expects quotation marks.

    moviebot> Sorry, I don't understand. Tell me about a movie that you have seen.
    I really liked "Moneyball".
    moviebot> You liked "Moneyball". Thank you! Tell me about another movie you have seen.
The bot understands everything, so it echoes your sentiment. It’s a bit repetitive, though.

    > I was completely amazed at how "Rush Hour 3" was pretty much the most ridiculous thing I've seen in my entire life.
This sentence was too hard for the starter bot. That’s okay!

    moviebot> I'm sorry, I'm not quite sure if you liked "Rush Hour 3". 
    Tell me more about "Rush Hour 3".
    > I hated "Rush Hour 3".
    moviebot> You did not like "Rush Hour 3". Thank you! 
    Tell me about another movie you have seen.
    > Oh, I also thought "The Little Mermaid" was fantastic!
    moviebot> You liked "The Little Mermaid". Thank you! Tell me about another movie you have seen.
    > I'm tired of typing about movies.    
The bot gives a nice response to this! It’s okay for the chatbot to require 5 data points before giving a recommendation:

    moviebot> I want to hear more about movies! Tell me about another movie you have seen.
    I didn't like "The Notebook" or "Avatar".
The bot can’t handle this, but that’s okay.

    moviebot> Please tell me about one movie at a time. Go ahead.
    I didn't like "The Notebook".
The bot makes a mistake in sentiment extraction in its reply below. This was a pretty straightfoward sentence, so this mistake should be penalized.

    moviebot> You liked "The Notebook". Thank you! 
    That's enough for me to make a recommendation.
    I suggest you watch "In the Heart of the Sea". 
    Would you like to hear another recommendation? (Or enter :quit if you're done.)
    > :quit
    moviebot> Thank you for hanging out with me! Stay in touch! Goodbye!
It’s up to you to provide appropriate guidance for the user, so that they give you the output you expect, and to make your chatbot as natural and as engaging a speaker as you can. The following section gives you a high-level perspective of a possible API.

# Writing code for starter mode
The REPL class calls the process method in the Chatbot class each time the user enters a string. Your implementation of process should handle the input, update internal data structures accordingly, and output a nice response for the user.

### Implementing core logic: `extract_titles`, `find_movies_by_title`, `extract_sentiment`, and `recommend`
We've decomposed some of the core logic for you. You will need to implement the following four methods, per the specifications [in the rubric](#evaluation):

`extract_titles(preprocessed_input)`: given an input text, output a list of plausible movie titles that are mentioned in text, i.e. substrings for the bot to look up in the movie database. For starter mode, this function should simply output all substrings that were found between quotation marks.

`find_movies_by_title(title)`: return a list of indices corresponding to titles in the movie database matching the given title. As a baseline, you are expected to handle:
- Titles with or without the year included
    - If a year is not provided, a title may match multiple
    - find_movies_by_title("Titanic") should return [1359, 2716], corresponding to the 1997 and 1953 versions
    - find_movies_by_title("Titanic (1997)") should return simply [1359]
- The way many movies in the database have English articles (a, an, the) moved to the end. 
    - For example, find_movies_by_title("The American President") should return [10], where 10 is the index of "American President, The (1995)". 

`extract_sentiment(preprocessed_input)`: extract the sentiment of text. For starter mode:
- return -1 if text has negative sentiment
- 1 if positive sentiment
- 0 if no non-neutral sentiment detected.
- If you're not sure where to get started, check out [the provided lexicon](#your-sentiment-lexicon) and think about how you might use this with techniques learned on previous PAs!

`recommend(user_ratings, ratings, k)`: This function should:
- Input the **provided vector of the user's preferences** and a **pre-processed matrix of ratings by other users** (See the following section for details on pre-processing)
- Use collaborative filtering to **compute and return** a list of the **k movie indices** with the highest recommendation score that the user hasn't seen. 
- For starter mode, you will implement and use item-item collaborative filtering, with **cosine similarity** and **NO rating normalization**.

`binarize(ratings, threshold)`: Binarizes the ratings matrix (for more details, see the [movie database section](#your-movie-database-movielens)). This function should:
- Replace all entries **above the threshold** with **1**
- Replace all entries **below the threshold** with **-1**
- Leave entries that are 0 as 0
- Be called at the **beginning of init** 
    - You will be working with a binarized ratings matrix for the rest of the assignment!

These functions will be tested by the autograder, so make sure you implement them correctly! The starter code includes a sanity check script that runs some basic tests on these functions to help you debug them. Once you've debugged locally, we **highly reccomend** you submit to Gradescope to test these with the autograder. 

We advise that you explore these and other function stubs provided in the starter code and call them in process as appropriate.

### Combining your functions in `process(line)`
Once you have implemented the above functions, you can integrate them into `process(line)`, which crafts a response for a given input. 

Unlike the autograder, your users won't be able to read the output of the helper functions above to tell if you extracted the right sentiment and movie title. To give them a chance to correct you, your bot is **required** to echo the sentiment and the movie title that it extracts from the user’s input. You can do this creatively; see the example above for one possibility. **If you don’t echo this information, you cannot get maximum points for title extraction and sentiment extraction.**

There are many different ways to implement modules (2) (extracting relevant information from the input) and (3) (crafting relevant information into a response) for the chatbot. Read the [rubric](https://docs.google.com/spreadsheets/d/1v1f4moRUXAmA3zzjgaBIiq-LJTgaSBCHbWTZvDta808/edit?usp=sharing) carefully to see the functionality that will be evaluated, and implement that first! The next two sections give you information on the data provided in the starter code.

# Your movie database: MovieLens
The data comes from [MovieLens](https://movielens.org/) and consists of a total of 9125 movies rated by 671 users The starter code provides a 9125 x 671 utility matrix that contains ratings for users and movies. The ratings range anywhere from 1.0 to 5.0 with increments of 0.5. For starter mode, you will implement binarize method and use it to binarize the ratings as follows:

    +1 if the user liked the movie (3.0-5.0)
    -1 if the user didn’t like the movie (0.5-2.5)
    0 if the user did not rate the movie
The starter code also provides a list with 9125 movie titles and their associated movie genres. The list is formatted as expected: the movie in the first position in the list corresponds to the movie in the first row of the matrix. An example entry looks like:

    ['Blade Runner (1982)', 'Action|Sci-Fi|Thriller']
As in previous homeworks, you can inspect the data to have an idea of the variations in how the titles are represented in this dataset. Note that the second element in the list/array is a string with movie genres separated by the pipe symbol |. You may find this information interesting for the creative mode!

## Your sentiment lexicon
The starter code also provides a sentiment lexicon that you can use to extract sentiment from the input. It consists of 3624 words with their associated sentiment extracted from [Harvard Inquirer](http://www.wjh.harvard.edu/~inquirer/spreadsheet_guide.htm) (Stone et al. 1966). The lexicon is stored for your convenience in a dictionary/hash map, where the word is the key and the sentiment the value.

## Sanity check
We have provided a sanity check script to test your code on basic inputs. To run the starter mode sanity checks, run `python testing/sanitycheck.py`
### Local script testing
As mentioned before, you can run individual test scripts located in the testing/test_scripts/ directory with the following command:

    python repl.py < testing/test_scripts/simple.txt
You can also run all the scripts with the following commands:

    cd testing/
    sh run_all_scripts.sh
You should check the outputted transcripts to make sure that your chatbot doesn't crash or fail incoherently. We will not be grading on these exact scripts (nor do the given test scripts cover all the requirements given in the rubric), but the method of running scripts will be used. Feel free to add your own test scripts as well! If you do, don't forget to add :quit as your last line or else the script will never terminate.

**Note:** passing locally does **NOT** guarantee that you will pass the autograder, so please submit **early and often**!!

# Printing debug info
The debug method in the Chatbot class can be used to print out debug information about the internal state of the chatbot that you may consider relevant, in a way that is easy to understand. This is helpful while developing the assignment, but will not be graded. To enable debug info, type in the chatbot session in the REPL

    :debug on

and type

    :debug off

to disable it.

# Important Notes and Tips
To ensure your recommend function's output matches the autograder's, make sure to implement collaborative filtering as described in the comments! As a reminder: item-item collaborative filtering, cosine similarity, and no rating normalization.

We encourage you to use numpy for this assignment. [np.dot](https://docs.scipy.org/doc/numpy/reference/generated/numpy.dot.html), [np.linalg.norm](https://docs.scipy.org/doc/numpy/reference/generated/numpy.linalg.norm.html), and [np.where](https://docs.scipy.org/doc/numpy/reference/generated/numpy.where.html) may be especially helpful.

# Creative Mode (45 points)
After you have implemented the movie recommendation chatbot in the starter mode, it is time to switch to creative mode! In creative mode your chatbot can be a better movie recommender system, but you can also take it in a different direction if you want to. This time the user will challenge the chatbot with unexpected input and evaluate it on different advanced capabilities. Refer to the [rubric](https://docs.google.com/spreadsheets/d/1v1f4moRUXAmA3zzjgaBIiq-LJTgaSBCHbWTZvDta808/edit#gid=0) to see how the chatbot will be evaluated, which will give you ideas about capabilities you can implement. There are 78 points worth of features to choose from, out of which 45 will be counted. (So if you implement 60 points worth of features, your score on this section will be capped at 45.)

In rubric.txt, you must mark all the functionality you implemented in creative mode, which you can do by replacing the "NO" with "YES" in the desired lines. Requirements marked as "NO" will not be graded.

In the provided section, please also include a short description of what each team member contributed or state that everyone contributed equally if you believe this is the case.

# Switching to Creative Mode
Switching between starter and creative modes is easy: by default, the REPL starts in starter mode. To switch to creative, type the following:

    python repl.py --creative
To run the creative mode sanity checks, type the following:

    python testing/sanitycheck.py --creative
To run the test scripts in creative mode, type the following with the desired script path:

    python repl.py --creative < testing/test_scripts/simple.txt

## Difference between starter mode and creative mode
The difference between starter mode and creative mode is fundamentally a difference in how you will be evaluated.

If your creative mode interpretation does not meet the starter mode requirements (either because it is not a movie recommender, or because it does movie recommendations in a different way), then you need to implement a logical fork -- remember that you can check which mode is running with the self.creative variable.

But if your creative-mode chatbot meets all the starter mode requirements, you don’t need to dumb it down for starter mode evaluation! You can have a seamless implementation that runs in both modes, and you will still get credit in both modes.

Before submitting, please test with the scripts in testing/test_scripts/ for both starter and creative mode to make sure they do not fail. If the grading scripts fail, you will not get points for the associated requirement. For example, the rubric item "Failing gracefully" is marked as a starter mode requirement, so any associated script will be run without the --creative flag.

## Other resources and code size
Numpy is encouraged, but please do not assume other libraries that are not in the standard Python 3.8 distribution in your implementation.

Although with the data provided you can be really creative, you are free to use the resources that you may want as long as they are integrated in the class chatbot.py. We will also optionally accept a file named PorterStemmer.py and a deps folder containing other files (we won't accept additional folders inside deps). The maximum size of all files that you will submit is strictly 100KB, so be careful if you add resources!

Note: To avoid problems with submission, please make sure that the names of your files/folders exactly match the specifications above. That is, your optional deps folder must be named deps, and your optional PorterStemmer.py file must be named PorterStemmer.py. Both items (if included) must be in the same assignment directory with chatbot.py.

## Ethics question
Also included in `rubric.txt` is an ethics question that we would like you to answer about Chatbots. It is worth 2 points!

## Evaluation
The TAs will grade your submission with respect to the [rubric](https://docs.google.com/spreadsheets/d/1v1f4moRUXAmA3zzjgaBIiq-LJTgaSBCHbWTZvDta808/edit#gid=0) by providing your chatbot with a standardized series of inputs. 

There are 136 points total in the rubric: 
- 60 points for starter mode features
- 76 points to choose from among the creative mode features, out of which you can earn at most 45 points
- 2 points from the ethics question in `rubric.txt`. 

You will need 97 points (95 from some combination of starter and creative components, 2 from the ethics question) for full credit. Note: although you can choose which creative components to implement, the ethics question **is required**.

Up to **10 additional points** will count as **extra credit**.

## Submitting Your Solution
Submit your assignment via Gradescope (www.gradescope.com). We expect the following files in your final submission:

    chatbot.py
    rubric.txt
If you add any additional files, create a deps/ folder and put your additional files in this folder. The autograder might not work if you format your files incorrectly.

To create a .zip file for submission:

    sh generate_submission.sh

Upload the submission.zip file that shows up to Gradescope. This will zip up chatbot.py, rubric.txt and optionally, your deps folder.

Alternatively, if you don't submit a deps folder, you're also welcome to directly drag chatbot.py and rubric.txt into Gradescope without zipping. Note that if you used the Porter stemmer in porter_stemmer.py, you don't need to do anything special related to that. It will be accessible to your chatbot from inside the autograder.

We are enabling group submission. After you submit the required files, you'll see "Add Group Member" in the top right of the Autograder Results page. Please add the group members you worked with (you may add up to 3). Note that only one group member needs to actually upload the submission to Gradescope.

### Autograding vs Manual grading
The autograder will test and grade each function in the [rubric](https://docs.google.com/spreadsheets/d/1v1f4moRUXAmA3zzjgaBIiq-LJTgaSBCHbWTZvDta808/edit#gid=0) except process, which the TAs will be grading manually. The TAs will only manually grade creative features you marked in your rubric.txt, and as a reminder to do so we included a 1-point check in the autograder ("Part 0"); if it gives you a 0.0/1.0, that means your rubric.txt hasn't been edited properly. Your full score will be released after the TAs finish grading your submission.
