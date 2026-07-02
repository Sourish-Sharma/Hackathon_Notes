## CyLab RE questions

- FlagHunters
    - Python script given
    - found that the input wasn't sanatized as it was directly concatenated/added, and it splits using the ; character
    - Saw a function which returned verses from 0 to 9, as we needed 0, we input RETURN 0, but that would have been returned a string, so we use `text;RETURN 0` as the split happens from ;

