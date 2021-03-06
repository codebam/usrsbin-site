---
title: The First Spellchecker (In 6 Lines)
author: Alex Beal
date: 03-12-2011
type: post
permalink: The-First-Spellchecker--In-6-Lines-
---

I recently came across this gem in a book called [Classic Shell Scripting](http://www.amazon.com/Classic-Shell-Scripting-Arnold-Robbins/dp/0596005954/). According to the authors, it's a modern port of the first spellchecker. The original version was written by Steve Johnson, and was then translated into the form you see here by Kernighan and Plauger. I assume the translation was necessary because the syntax of the original isn't compatible with modern shells. Without further ado, here it is:

    cat filename | 
      tr A-Z a-z | 
        tr -c a-z '\n' | 
          sort | 
            uniq | 
              comm -13 dictionary_file -

It's a spellchecker, which, at 115 characters, fits within a tweet! Imagine that! To prove to you that it actually works, and to truly appreciate the cleverness of this code, let's step through it line by line. The first command, 'cat', simply reads the file and sends it down the pipe. This is then processed by 'tr' which converts all the uppercase characters to lower case. This is again piped to 'tr', but this time everything that's not a lower case character is converted to a newline character. At this point, the original input has been processed down to a list of words separated by newline characters. This list is then sorted alphabetically with the 'sort' command, and all the duplicate words are removed with the 'uniq' command. Finally, this list of words is compared with a dictionary file. Any words that appear in the list, but not in the dictionary, are sent to standard output. The final result is a list of words that appear in your document, but not in the dictionary, and thus are probably misspelled. At this point, you may have noticed a rather serious problem with this script. It doesn't do a very good job of handling apostrophes. The third line will remove all single quote characters, even if they're functioning as apostrophes, and replace them with newline characters. One way to fix this is to replace line 3 with the following:

    sed -r "s/('$|^'|\W'\b|\b'\W|\W'\W)/ /g" |
      tr -c a-z\' '\n' |

That rather ugly 'sed' command will remove all the single quote characters which aren't in the middle of words, thereby preserving apostrophes, and the new 'tr' command will ignore all single quote characters since 'sed' has already dealt with them. The end result is that the apostrophe in, for example, `foobar's` will be preserved but the single quotes at the beginning and end of `'foobar's'` will be removed. Another improvement would be to keep it from removing hyphens in compound words, but I'll leave that as an exercise to the reader. Also, If you need a dictionary file try:

    sudo apt-get install wamerican-small

And look under /usr/share/dict/words.

    $ wc -l words
    50253 words

That's a lot of words! Try 'wamerican-insane' if you want a, well, insane number of words.
