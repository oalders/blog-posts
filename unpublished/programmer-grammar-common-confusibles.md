# Programmer Grammar: Common Confusibles

Common confusables are words which we often mistakenly exchange for one another. Some common examples would be "its" vs "it's", "mold" vs "mould" or "affect" vs "effect". These are generally words which are strongly similar in spelling and are similar sounding.  A spell checker won't always catch you misusing these words. Often these mistakes are only caught by another human being.

Now, what does this have to do with programming? I've come to notice a few obvious confusables which tend to trip programmers up. I include myself in the group of people who have made these mistakes and I like to share what I've learned.

During code reviews at work, we've traditionally had a lot of emphasis on naming things correctly, spelling correctly and just generally getting things right. It's in this context that I'd like to raise a few points of grammar which can make your code (and your documentation) easier to understand and also just a little more correct. The point of this exercise is to point out how being consistent about how we use words can increase the clarity of the code which we write. It's also worth pointing out that these mistakes will be made by both native and non-native English speakers.

## Problem #1: Nouns and Verbs in Subroutine Names

In many cases, it's helpful to have a subroutine name which reads like English. For example:

* `make_me_a_sandwich()`
* `authenticate_user()`
* `delete_session()`

Using this kind of a naming scheme makes it obvious what your subroutine does. If you're going to do this, it helps to be consistent, as that will lead to the least confusion.

The examples above all follow a `verb + noun` pattern and each example stands on its own as a proper sentence.  How about this name?

###`setup_user_account()`

Do you see the issue? The problem here is that **setup** is a noun, so what we see above is 3 nouns jammed together. Subroutine names are generally easier to understand when they have a verb, because that means they express what kind of action they are going to take. Let's take a look at the subroutine name with a verb in place of the noun:

### `set_up_user_account()`

This is clearer and easier to read, because it pretty much works as an actual sentence. Simply by seeing the name of the subroutine, we have an idea of what it should do. Let's look at another example.

### `login_user()`

What does this subroutine do? Does it return the id of a user which can be used for a login? Does it actually log a user in? It's not clear.

### `log_user_in()`

is more obvious. It's a subroutine used to perform authentication.

## Problem #2: Combining Nouns and Verbs in Class Names

We've seen above that it's *generally* nice to use subroutine names which contain a verb, since they tell you what the subroutine wants to do. It's also nice to have class names which are strictly nouns, since they give a name to the thing that contains the subroutines. Using this as a guideline, if you are CamelCasing your class names, you'll want to consider this: `Model::User::SetUp` If we break down the above, we get: `Noun::Noun::Verb`.

A class name made strictly of nouns is not going to boil down to a readable sentence -- it doesn't have to. However, stringing together a random collection of nouns and verbs isn't going to improve clarity about what the class can do. If we treat the class name as a collection of nouns we get: `Model::User::Setup` or `Noun::Noun::Noun` Our class naming now follows a consistent pattern and is less confusing.

Your mileage may vary with the above advice.  In fact, you may reject it completely.  However you decide to name your classes, there can be a lot of value in consistency.  Keep in mind that the mere act of taking the time to consider these issues when creating class names can lead to more readable code.

## Problem #3: Nouns and Verbs on Buttons

In a UI, a button is a call to action, so I think it makes sense to tend towards verbs when adding text to a button. The classic default for a button is **submit** (verb) as opposed to its corresponding noun: **submission**. Taking this as a guideline, you probably want to prefer "log in" over "login", "log out" over "logout", etc when naming buttons.

## Problem #4: Mixing Nouns and Verbs in Menu Items

I don't have a strong opinion on when to use **login** vs **log in** in menu items on a web page, but if your menu items are generally nouns, I would stick with the noun (**login**). If your menu items are generally verbs, I would stick with a verb (**log in**).

The occasion where a hard rule should be applied is in a *Call to Action*, ie if you're actually telling (or inviting) the user to do something. **Signup now** is not strictly correct, since it contains no verb and doesn't really imply a verb. **Sign up now** is a call to action which is clearly understood, so I would prefer it in this case.

As a very general example, a menu of mostly nouns might look like:

| Contact Info | About Us | Login | Support |
|--- |--- |--- |---

A menu made up of verbs (calls to action) might look like:

|Contact Us | Learn About Us | Log In | Get Support|
|--- |--- |--- |---

I'm not proposing hard and fast rules for menu items, but I do think it's helpful to consider nouns vs verbs when putting them together.

## Noun and Verb Cheat Sheet

This is by no means complete list of nouns and verbs which can trip us up, but it's a good starting point.

|Noun|Verb|
|--- |--- |
|backup|back up|
|breakup|break up|
|cleanup|clean up|
|login|log in|
|logout|log out|
|lookup|look up|
|markdown|mark down|
|markup|mark up|
|setup|set up|
|signup|sign up|
|signin|sign in|
|signout|sign out|
|wipeout|wipe out|

You will likely have noticed a pattern here.  If you're dealing with a word ending in up, down, in or out, you may want to take special care to ensure that you're using it correctly.

## Bonus Grammar

While we're at it, let's go over a couple of other commonly confused words which don't belong to the noun vs verb pattern.

### Deprecated vs Depreciated

Many times I've read documentation which says that a feature has been _depreciated_. Unless your code is dealing with finance, you almost certainly meant to say that the feature has now been _deprecated_. The presence or absence of the letter _i_ in this word has a huge impact on the meaning.

### Compliment vs Complement

If you're comparing lists of data, you probably want to say _complement_. If you're telling me I look great in this suit, you're giving me a _compliment_.

### Pore vs Pour

You _pore_ over code (that is, you study it carefully). You don't _pour_ over code, unless your dumping a cup of coffee onto it.

## Sharing What You've Learned

If you see someone else making one of these mistakes, I encourage you kindly to correct them. Being sensitive when correcting someone else's grammar is very important. We all make grammatical mistakes, whether it's because we're tired, in a hurry or just not aware of the rules. It's possible to be kind while helping someone improve their grammar. Also, if you're inelegant about pointing out grammatical errors, it's the kind of thing that people remember about you, and not in a good way.

The point of this exercise is to make code clearer and easier to read.  The easier your code is to understand, the easier it is to maintain for everyone who follows -- including future you.
