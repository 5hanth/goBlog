+++
title = "Hello Hakyll"
date = "2014-10-17"
tags = ["hakyll"]
categories = ["programming"]
+++

It's been a long time since I blogged. All these days I were doing
random things whatever that came in my way.  I had been hooking up
with [Haskell](http://haskell.org%20) past few months. I came back to blogging not just to
track down all my brain vomits, but also to mess up with [Hakyll](http://jaspervdj.be/hakyll%20) static
site generator.

Hakyll is effin' awesome ! It has got a lot of features that makes
blogging that easy and geeky :D

Now I can write code illustrations:

```.haskell
main :: IO ()
main = getLine >>= putStrLn . (++" !") . ("Hello"++)
```

Or whatever I still have to explore :) and that too with less
distress.

And now I wanna find a hosting and setup with my domain.

- Folks say that Dropbox and GDrive offered a way a while back which
no longer works :(
- git / gitorious / gitlab / heroku / rhcloud ???
- Its obvious that using a Version Control System is right choice as
  blogging using a static site generator involves :
	- Writing Markdown
	- Re-Compiling to Generate Pages
	- Sync with hosting
- If not a Version Control System, then I may have to do something
  with rsync or csync to update remote pages.
- But I still hesitate to put all my personal babblings to [Github](http://github.com/5hanth)
- *Hmm.. 've to decide yet.. *
