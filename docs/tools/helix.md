

 * Type w to select forward until the next word.
   * Type e to select to the end of the current word.
   * Type b to select backward to the start of the current word.
   * Use uppercase counterparts, W,E,B, to traverse WORDS.

 * Type d to delete the entire selection.
   * Type c to delete the selection and enter Insert mode.

 * Type a number before a motion to repeat it that many times.

 * Type v to enter Select mode, where all motions extend the
   selection.

 * Type x to select the entire current line. Type x again to
   select the next line.

 * Type semicolon ( ; ) to collapse selection.



   * Use Space + y and Space + p to yank / paste on the system
     clipboard.

 * Type C to duplicate the cursor to the next suitable line
   and Alt-C for previous suitable line.

 * Type s to select all instances of a regex pattern inside
   the current selection.

 * Type & to align selections.

 * Press Alt-s to split the selection into lines.


 * Type f / F to extend selection up to & including a character.
   * Type t / T to extend selection until a character.

 * Type r to replace selected characters.

 * Type . to repeat the last insertion.
   * Press Alt-. to repeat the last f / t selection.





 * Type R to replace the selection with yanked text.

 * Type J to join lines in selection.

 * Type > and < to indent / unindent lines.

 * Press Ctrl-a to increment the selected number.
   * Press Ctrl-x to decrement the selected number.


 * Type n / N in Select mode to add selections on each search
   match.

 * Type gw to enable 2-character labels, and any 2 characters to
   jump to the corresponding label, or ESC to drop the labels.

 * Use Ctrl-c to comment a line under your cursor. Press Ctrl-c
   again to uncomment.
 * To comment multiple lines, use the selections
   and multi-cursors before typing Ctrl-c.
   * Commented lines cannot be uncommented but commented again.

 You can enter the match mode with the m key; this will show the
 actions available in a popup. This will allow you to:
  * jump to matching pair of delimiters with mm (you must have a
  delimiter belonging to a pair under your cursor)
  * select inside a pair of delimiters surrounding your cursor
  (i.e. select the content but not the delimiters) with mi(
  and similar
  * select around a pair of delimiters surrounding your cursor
  (i.e. select the content and the delimiters) with ma( and
  similar
  * delete surrounding delimiters with md( and similar
  * add surrounding delimiters around the selection with ms(
  * replace a pair of delimiters surrounding your selection with
  mr([ to replace for example surrounding () with []










