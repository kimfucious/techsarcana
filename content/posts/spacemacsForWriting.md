---
title: "Using Spacemacs as a (prose) Writing Environment"
date: "2019-09-05"
toc: true
images:
tags:
  - Spacemacs
  - Emacs
  - Writing
  - Latex
---
## Why?!

tl;dr:  I get frustrated using writing apps that don't support Vim style keyboard navigation.

If you're old, nerdy (in a computer sense) or both, you've probably come across Vim.  It's great, and there is no denying that keeping one's fingers on the home row makes one a more efficient inputer.

And there's emacs.  It's great too.  This is not an article about Emacs or Vim, but if you're interested in arcane nerd history, Google:  Bram Moolenaar, Richard Stallmand and Eric. S Raymond.

Over the years, I've tried out many writing programs, including: 

  - iA Writer
  - Mellel
  - Scrivener
  - Storyist
  - StoryMill
  - Ulysses

I've also used plenty O' word processers, including Microsoft Word.

:memo:  I'm writing this in [Markdown](https://daringfireball.net/projects/markdown/), using the setup described herein on a Windows 10 machine, running [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10), because my Mac died and I was feeling poor enough to buy a Windows box.

Each of the above applications have their own strengths, characteristics, idiosynchrasies, and weaknesses, but as far as I can remember, none of these products allow one to navigate like Emacs or Vim without any extra help.  And those things that can be considered extra help, like [Karabiner](https://pqrs.org/osx/karabiner/) and [AutoHotKey](https://www.autohotkey.com/), are imperfect solutions at best when compared with native navigation.

My favorite app, out of the lot, is Scrivener.  Unfortunately, on Windows (at the time of this writing), they are stuck on version 1 and have been delaying their move to version 3 for some time now.  And, when it is released, I'm pretty sure that it won't have Emacs or Vim-like navigation, although that's been a running joke for a while now.

So the answer to "why" is another question...

## How can I get Scrivener-like features with Emacs or Vim-like navigation?

TL;DR:  [Spacemacs](http://spacemacs.org/) with [Org mode](https://orgmode.org/) and [Tex](https://texfaq.org/FAQ-whatTeX)/[Latex](https://www.latex-project.org/about/)

Technology is about solving problems right?  _Ha ha ha!_ 😂 Let's see about that...

All of the below is based on a Windows, non-WSL install.  WSL instructions to follow.  

### Initial Setup

1. [Install Git](https://www.atlassian.com/git/tutorials/install-git)

    Git is required here in order to clone Spacemacs "into" Emacs.  It's not technically _required_, as you can expand a zip of the repo in step 7 below, but Git's also nice to have for other things.  

2. [Install Adobe Source Code Pro Font](https://github.com/adobe-fonts/source-code-pro)

    Warnings are thrown if you don't have this font loaded in your OS.  Things will mostly work without it, but there may be issues with certain things displaying incorrectly.

3. [Install Emacs](https://www.gnu.org/software/emacs/download.html)

  - Download the latest version of Emacs from [here](https://www.gnu.org/software/emacs/download.html).  At the time of writing, the file name was `emacs-26.3-x86_64.zip`. 
  - Put the contents `C:\Program Files\emacs`, or wherever you want.
  - Create a shortcut of the `runemacs.exe` file located under `C:\Program Files\emacs\bin`.
  - Edit the properties of that shortcut and change the `Start in` field to where you want your default directory to be when Emacs/Spacemacs launches (e.g. `C:\Users\YOURNAME\Documents`).
  - Move that shortcut somewhere easy to click on and/or assign a shortcut key to it for easier access.
  
        Something like this:
    
    {{< figure src="../../images/emacs_shortcut.png" title="Emacs Shortcut Example" >}}

4. [Add Spacemacs](https://github.com/syl20bnr/spacemacs/blob/master/doc/BEGINNERS_TUTORIAL.org)

  - Navigate to `C:\Users\YOURNAME\AppData\Roaming` where you will store the Spacemacs configuration.  Your drive letter may vary, depending on your setup.
  - Run the following command (using a terminal that works with Git on Windows) to clone Spacemacs into a new directory, `.emacs` in that location

        ```console
        git clone -b develop https://github.com/syl20bnr/spacemacs .emacs.d
        ```

        :point_up: Note that the above repository link is for the `development branch`.  Oddly enough, this is the "preferred" branch to use, as it actually works "better" than the master branch.  This post and its instructions are based on the development branch.

### ✔ Checkpoint

Okay, at this point, when you launch Emacs you should see Spacemacs.  You'll be asked two questions.  

{{< figure src="../../images/spacemacs_initial_setup.png" title="Spacemacs First Launch" >}}

I recommend the following responses:  

1.  Select Vim for the preferred style, unless you know you really want Emacs.
2.  Select the standard distrubution.

Spacemacs will now start to load and configure a bunch of packages.  Wait for it...

:bomb:  Problems?  

- If Windows doesn't like what you're doing, trying to launch an app from an unknown publisher, tell it to Run Anyway. 

{{< figure src="../../images/defender_warning.png" title="Windows Defender Warning" >}}

- Spacemacs hanging?  I didn't have this issue on Windows (only on WSL), but you could have a problem with Spacemacs not being able to use HTTPS.  You'll need to use the `--insecure` flag.  See [Spacemacs is Hanging on Load](#spacemacs-is-hanging-on-load) below for more info.

:tada:  Success!
When you're all done, you should see something like this:

{{< figure src="../../images/spacemacs_initial_splash.png" title="Spacemacs Initial Splash Screen" >}}

### Additional Setup

The way to configure Spacemacs is by editing the `.spacemacs` dot file. 

If anything goes awry while working in this section, see the [Troubleshooting](#troubleshooting) section below.

You can open this file, using `helm find file` by typing the `SPC f f` key sequence and entering `C:\Program Files\YOURNAME\AppData\Roaming\.emacs.d\.spacemacs` or wherever you put it in step #4 of [Initial Setup](#initial-setup) above. 

You'll see that the file is written in Lisp. If that makes you feel uncomfortable, don't worry.  We're only going to change a few things.  Just be careful not to delete anything you're not supposed to :scream:

#### Add Configuration Layers

1.  Find the line that reads `dotspacemacs-configuration-layers`.  
2.  Uncomment (remove ';;' from) the lines where you see `markdown` and `org`.
3.  Optional: add `themes-megapack`

Your original dot file may be different from what mine was, so just try to get yours to look a bit like the below.  I've left the original comments in the dot file.  No need to be concerned about these (or those in yours) at the moment:

{{< highlight lisp "hl_lines=13-14,linenostart=108" >}}
dotspacemacs-configuration-layers
'(
  ;; ----------------------------------------------------------------
  ;; Example of useful layers you may want to use right away.
  ;; Uncomment some layer names and press <SPC f e R> (Vim style) or
  ;; <M-m f e R> (Emacs style) to install them.
  ;; ----------------------------------------------------------------
  helm
  ;; better-defaults
  emacs-lisp
  ;; git
  ;; latex
  markdown
  org
  ;; (shell :variables
  ;;        shell-default-height 30
  ;;        shell-default-position 'bottom)
  ;; spell-checking
  ;; syntax-checking
  themes-megapack
  ;; version-control
  )
{{< /highlight >}}

There's a bunch of other stuff we could add here, but let's keep it simple for now.

:stop_sign: Don't be tempted to enable other things (e.g. spell-checking) at this point.  Spell-checking and others will be covered later.

Save these changes using the `SPC f s` key sequence and restart Spacemacs, using `SPC q R`.  

You should see the new packages being loaded upon restart, although it may happen quicker than the eye.  


### Customizations 

There are a some settings that we can change to customize the Spacemacs initialization process.  These are optional.

The following changes will be made in `defun dotspacemacs/init()`.  This is a function, so understand that breaking syntax will mess things up.  Be sure not to add or remove anything like colons, paraens, or quote marks unnecessarily, for example.

#### Maximize on Startup
Spacemacs opens in a smallish window upon start.  Set it to maximize on start with the below:

Locate `dotspacemacs-maximized-at-startup nil`, and change `nil` to `t`:

```lisp
dotspacemacs-maximized-at-startup t
```

#### Change the font size

I found the default font-size to be too small for my tastes.  Adjust to your desirement.  

Locate `dotspacemacs-default-font, and change it, like so:

{{< highlight lisp "hl_lines=2,linenostart=161" >}}
dotspacemacs-default-font '("Source Code Pro"
                        :size 18
                        :weight normal
                        :width normal
{{< /highlight >}}

#### Set numbers on as default

I like line numbers in my text editor.

Change dotspacemacs-line-numbers from `nil` to `t`: 

```lisp
dotspacemacs-line-numbers t
```

### ✔ Checkpoint

You should now be able to create an `.org` file like the below in Spacemacs.  Note the bullets, this a good sign that Org Mode is working.

{{< figure src="../../images/spacemacs_org_example.png" title="Spacemacs Org Mode Example" >}}

:unicorn: See the [resources section](#resources) for a brief video overview of [Org Mode in Spacemacs](https://orgmode.org/) 

### Setup Spell-checking

This setup uses [Hunspell](http://hunspell.github.io/) and [FlySpell](https://www.emacswiki.org/emacs/FlySpell) and *requires* that you are using the `development` branch of Spacemacs.  

FlySpell is nice because it will highlight spelling errors "on the fly".

:book: Note that I've only included an American English dictionary (en_US).  Adjust to your requirements. 

1. Download [Hunspell](https://sourceforge.net/projects/hunspell/)
2. Extract the zip file contents to `C:\Program Files\hunspell` or wherever you want it to go.  If you use something different, modify the exec-path in the second code block below accordingly.
3. Open the `.spacemacs` dot file for editing.

⚠️ Be careful not to mangle syntax when editing the `.spacemacs` dot file!

#### Under `dotspacemacs-configuration-layers`

Replace the commented out `;;spell-checking` line with the following code block:

```lisp
(spell-checking :variables
                spell-checking-enable-auto-dictionary t
                spell-checking-enable-flyspell-auto-completion t)
```

#### Under `dotspacemacs/user-init()`

Add the following code block:

```lisp
(with-eval-after-load "ispell"
  (add-to-list 'exec-path "C:/Program Files/hunspell/bin/")
  (setq ispell-program-name (locate-file "hunspell"
        exec-path exec-suffixes 'file-executable-p))
  (setq ispell-program-name "hunspell")
  (setq ispell-dictionary "en_US")
  (setq ispell-local-dictionary-alist
        '(("en_US" "[[:alpha:]]" "[^[:alpha:]]" "[']" nil nil nil utf-8))))
```

:point_up: Note that this code block uses `exec-path`, which obviates the need to add `hunspell.exe` to your system PATH.

Once done, restart Spacemacs (`SPC q R`)

### ✔ Checkpoint

You should see indications of misspelled words in your files now.

{{< figure src="../../images/spellfly_example.png" title="SpellFly example" >}}

:memo: See section 3 of [this document](http://develop.spacemacs.org/layers/+checkers/spell-checking/README.html) to start learning spell-checking key bindings.

## Exporting to PDF 

Ultimately, after we're all done writing something worth sharing, we want to print it out or get it into a format that we can send to someone (like an agent).

1. [Install MikTex](https://miktex.org/)
  - MikTex contains the executable, `pdflatex.exe`, which is needed later when trying to export files from Spacemacs.
  - You need to make sure that this file is in your path, which is `C:\Program Files\MiKTeX 2.9\miktex\bin\x64` on a Windows installation (see [resources](#resources) at the end of this post for how).  Your drive letter may vary, depending on where you installed it.
  - While potentially overkill, just to get one's hands on `pdflatex.exe`, you might find yourself diving deep into [Tex](https://texfaq.org/FAQ-whatTeX)/[Latex](https://www.latex-project.org/about/) later, when you want to produce "beautiful books."
2. [Install Zip](https://sourceforge.net/projects/gnuwin32/files/zip/).
  - Amazingly enough, this executeable and its sister, `unzip` (below) from 2005, are necessary in order for Emacs to export files into other file formats such as ODT and PDF. 
  - I extracted and placed the contents at `C:\Program Files (x86)\GnuWin32\bin`
  - All executables need to be in your PATH in order for Emacs to see them.
  - To confirm this from the Windows side, open CMD or Powershell and try to run the file (e.g. zip.exe) anywhere *not* in the directory where it's installed.
3. [Install Unzip](https://sourceforge.net/projects/gnuwin32/files/unzip/)
  - I extracted and placed the contents at `C:\Program Files (x86)\unzip\bin`
  - Again, all executables need to be in your PATH in order for Emacs to see them.
4. Open the `.spacemacs` dot file for editing.
5. Add `latex` to `dotspacemacs-configuration-layers` per the below, and restart Spacemacs (SPC q R).

    {{< highlight lisp "hl_lines=12,linenostart=268" >}}
    dotspacemacs-configuration-layers
    '(
      ;; ----------------------------------------------------------------
      ;; Example of useful layers you may want to use right away.
      ;; Uncomment some layer names and press <SPC f e R> (Vim style) or
      ;; <M-m f e R> (Emacs style) to install them.
      ;; ----------------------------------------------------------------
      helm
      ;; better-defaults
      emacs-lisp
      ;; git
      latex
      markdown
      org
      ;; (shell :variables
      ;;        shell-default-height 30
      ;;        shell-default-position 'bottom)
      ;; spell-checking
      ;; syntax-checking
      themes-megapack
      ;; version-control
      )
    {{< /highlight >}}

6. Restart Spacemacs (SPC q R)

### ✔ Checkpoint

After that, you should be able to export your work to a PDF.  

Let's test it out:

1. If you haven't already, create an `.org` document.
2. Here's a starter.  Change it to your liking and save it:

    {{< highlight console >}}

    #+TITLE: Your Awesome Title 
    #+AUTHOR: Your Awesome Nom de Plume 
    * My Awesome Org File
      Isn't it neat?!
    ** My Awesome Subtopic{{< /highlight >}}

Don't fiddle too much here.  Formatting is a whole 'nuther can of worms.  We're just seeing if we can get it to work at this moment. 

3. With your modified `.org` document open, enter the following key sequence: `C-c C-e`.  This will bring up a text based menu, known as the `Export Dispatcher`.

    {{< figure src="../../images/export_dispatcher.png" title="Org Export Dispatcher" >}}

4. When you see this, type the following key sequence:  `l o`.  This will execute `Export to LaTeX As PDF file and open`.
    
    :bomb:  Problem?  See [Why Can't I Export?](#why-can-t-i-export) in the [Troubleshooting](#troubleshooting) section below for some help.

    :tada:  Success!  If you see your `.org` file has opened up in PDF format in your default PDF reader.

    {{< figure src="../../images/edge_pdf.png" title="A LaTex PDF file" >}}

## Troubleshooting

### Checking if Emacs can see an executable

There may be times when you're scratching your head (or banging it against a wall) because something isn't working.  Sometimes the reason that something doesn't work is because Emacs can't see the executable.  This is usually because the executable file in question is not installed and/or not in your system PATH.

To see if Emacs can see an executable, try the following:

1. Enter the keyboard sequence `SPC SPC` (i.e. hit space twice, it's called Spacemacs for a reason!).  This is the equivalent of `M-x`.
2. Type `eval expression` and press enter.
3. Type `(executable-find "zip")`, where `zip` is the name of the executable you are checking.

If you get a returned value like the below, Emacs can see the executable:

```console
"c:/Program Files (x86)/GnuWin32/bin/zip.exe"
```

If the response is `nil`, then Emacs can't see the executable.  See [resources](#resources) for information on how to set the PATH in Windows .

### Spacemacs is Hanging on Load

If Spacemacs is hanging on load, it could be because it's having trouble accessing the package manager via HTTPS.

:confused:  I've only experienced this when using WSL, not a straight Windows install.  And the only way I know around this is to disable HTTPS, either temporarily or permanently.

#### Temporarily disable HTTPS
You can get around this as a one-off by using the `--insecure` flag when issuing the `runemacs.exe` command, like this:

```command
Microsoft Windows [Version 10.0.17763.678]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Program Files\emacs\bin>runemacs.exe --insecure
```

:point_up:  Note that the command is being executed in the directory where it exists.  Otherwise, you'd need the directory in your path.

#### Permanently disable HTTPS
There's a couple ways to do this:

1. Add the `--insecure` flag to the windows shortcut, like this:

    {{< figure src="../../images/emacs_shortcut_insecure.png" title="Emacs Shortcut with Insecure Flag" >}}

2. Edit the `.spacemacs` dot file:
  - Locate `dotspacemacs-elpa-https t` in  `.spacemacs`, and change `t` to `nil`.
  - Restart Spacemacs with `SPC q R`.

### Re-jigging Spacemacs Packages

More than once, while working toward getting all this up and running, something unexplicably would not work.  Sometimes something as easy as restarting Spacemacs `M-q R` or "resyncing the configuration layers" `SPC f e R` would fix it.  A couple of times, it needed something a tad more potent.  

Short of re-installing, you can try the following, which can potentially fix package related issues that may occur along your journey.

Change to the `elpa` directory where you cloned Spacemacs:

  ```command
  cd [your user path where you cloned Spacemacs]/.emacs.d/elpa
  ```

Run

  ```command
  find . -name "*elc" -delete
  ```

Restart with `SPC q R`

### Why can't I export?!

1. If you're seeing somethng like the below when trying to export, there can be number of issues:

    {{< highlight console >}}
org-compile-file: File "c:/Users/YOURNAME/Documents/my_org_file.pdf" wasn’t produced.
See "*Org PDF LaTeX Output*" for details{{< /highlight >}}
    The first thing to check is whether or not Emacs can see all of the executables necessary to perform this operation.  These files are:

    - pdflatex.exe
    - unzip.exe
    - zip.exe

    If you open the `Messages Buffer`, you might be able to see the problem.  

    Try entering `:b *Messages*` in Spacemacs.  This should bring up the messages buffer.  Hunt around for any messages related to `pdflatex.exe, unzip.exe, or zip.exe`.  If there is an error or warning, you'll know where to start.

    Go back and revisit steps 1, 2, and 3 of the [Exporting to PDF](#exporting-to-pdf) section.  Re-check your work against these steps.

    To be sure that Emacs and thus Spacemacs can see these executables, see [Checking if Emacs can see an executable](#checking-if-emacs-can-see-an-executable).

    If Emacs can't see an executable, make sure that it's in your PATH.  See the [Resources Section ](#resources) for help on that.

2. This is a dumb one, but I do it more than I'd care to admit.  Exporting will fail if the PDF file is already open somewhere on your machine.  Check your browser, Adobe Acrobat, Spacemacs itself, or whereever else it might be open.  Close the file, if it's open, and run the export process again.

## Conslusion

- :100: free open source solution
- 😭 A relative pain to setup 
- 💁🏼‍♀️ Community-based support 
- 📵 [MobileOrg](https://mobileorg.github.io/) companion app
- 😵 Steep learning curve
- 😤 High probability of frustration (╯°□°）╯︵ ┻━┻
- 🤓 Ultra-high nerd factor
- :scream: Will ruin you for other apps than don't do Vim
- 🧭 Superior navigation
- 🛸 I want to believe

## Resources
- [How to Write a Book in Emacs](https://www.masteringemacs.org/article/how-to-write-a-book-in-emacs)
- [Installing Spacemacs on Windows](https://simpletutorials.com/c/2758/How+to+Install+Spacemacs+on+Windows) 
- [Migrating from Vim (to Spacemacs)](http://spacemacs.org/doc/VIMUSERS.html)
- [One author's Emacs setup for writing](http://tonyballantyne.com/EmacsWritingTips.html#orgheadline6)
- [Setting the PATH in Windows](https://www.computerhope.com/issues/ch000549.htm)
- [Spacemacs Spell Checking Layer](http://develop.spacemacs.org/layers/+checkers/spell-checking/README.html)
- [Spell-checking in Emacs](https://joelkuiper.eu/spellcheck_emacs)
- [What is Org?](https://orgmode.org/)
- 🎥 [Video: Using Org Mode in Spacemacs](https://youtu.be/S4f-GUxu3CY)