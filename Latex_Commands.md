 
# Latex Tutorial
 * You can read and edit your text file in every simple text editor.
 * For linux we can use "Tex Live" as a Text Distributions.
 * For linux we can use "Tex Maker", "Kile" and "Latexila" as a editor.
 * For a complete compile you need:
   1. Core
   2. Graphics
   3. Tools
   4. AMS_Latex 
--------------------------------------------------------------------------------------------
## A General structure of a Tex file is
```bash
   1. \documentclass[option1,option2,...]{class}
   2. Global Commands:
   .
   .
   3. \begin{document}
   .
   .
   4. Local Commands:
   5. \end{document}

 * The output will be a .tex
 * Example: 
 \documentclass{article}
 Hello World!
 \end{article}
 ```
-------------------------------------------------------------------------------------------- 
## Differnet class
 * article 
 * proc      
 * minimal    
 * report     
 * book    
 * slide   
 * letter
--------------------------------------------------------------------------------------------
## Options
 * size: 10pt, 11pt, 12pt
 * twoside or oneside 
 * paper size: a4, letter paper
 * draft
 * landscape
 * onecolumn or twocolumn
 * title page or notitle page  
-------------------------------------------------------------------------------------------- 
## Parts of a text based on their priority 
 * part --> -1
 * chapter --> 0 
 * section --> 1
 * subsection --> 2
 * subsubsection --> 3 
 * paragraph --> 4 
 * subparagraph --> 5 
 -------------------------------------------------------------------------------------------- 
## Different Environment of Latex
 * abstract --> For Abstract of articles and books
 * array --> for mathematical formulas e.g. Matrix
 * center --> Centeralize text 
 * description --> contains of some items and each item has a note (like dictionary)
 * document --> default environment 
 * enumerate 
 * equation
 * figure --> for adding figures 
 * flushright --> begin text from right 
 * flushleft --> begin text from left 
 * itemzie -->
 * letter -->
 * math --> for writing math formulas (shortcut is $$)
 * minipage -->
 * quotation
 * quote
 * table 
 * tabular
 * thebibliography
 * titlepage 
--------------------------------------------------------------------------------------------
## Tabular
```bash
 - \begin{tabular}{l|c|r}
     \hline
     Name & Age & City \\
     \hline
     Ali & 25 & Tehran \\
     \hline
     Sara & 30 & Isfahan
   \end{tabular}
```
or another way:
```bash 
 - \begin{tabular}{|c|c|c|}
     \multicolumn{2}{|1|}{Text} & - \\
     \hline
     \multirow{2}{0.5cm}{Q} & 2 & a \\
     & 4 & b\\
     \hline
   \end{tabular}
```
--------------------------------------------------------------------------------------------
## Graphics
 ```bash
 \usepackage{graphicx}      #--> 
 \DeclairGraphucExtensions{.pdf, .png, .jpg}       #--> 
 \includegraphics[options]{imagepath}       #-->
 ``` 
### the option of above can be: 
   1-width = x\
   2-hight = x\
   3-scale = x\
   4-keepaspectratio= true/false\
   5-angle = x\
   6-trim= l-b-r-t\
   7-clip= true/false\
   8-page= x
### General format is:
```bash
  \usepackage{graphicx}
  \DeclareGraphicsExtensions{.pdf,.png,.jpg}
  \begin{document}
  \begin{center}
  \begin{figure}[switches]
  	\includegraphics{scale=x}{Figure-Address/...}
  	\caption{text}
  	\label{figure.name}
  \end{figure}
  \end{center}
  \ref{figure.name}
```
 
### Subfigures in Latex General form:
```bash
  \usepackage{caption}
  \usepackage{subcaption}
  in a figure box (\begin(end){figure}:
  \centering
  \begin{subfigure}[b]{0.3\textwidth}
  	\centering
  	\includegraphics{scale=x}{Figure-Address/...}
  	\caption{text}
  	\label{figure.name}
  \end{subfigure}
```
#### Switches after `\begin{figure}[switches]` can be:
  - h : here
  - t : top
  - b : bottom
  - p : page
  - ! : ignore automatics of latex setting (override)
  - H : need to install \usepackage{float} ant its similar to h but a little better
--------------------------------------------------------------------------------------------
## Hyperlinks in Latex
```bash
 \usepackage{hyperref}
 \ref{label} --> \hyperref[label]{text}
 \hyperref[section.tex]{section \ref*{section.tex}} -->
```
### make a new command for above to make it simple:
```bash
 \newcommand{\myhyperref}[2][#2]{\hyperref[#1]{#2 \ref*{#1}}}
 ```
--------------------------------------------------------------------------------------------
## Bibliography
```bash
 \usepackage{cite} --> sort the referencees in the text
 \begin{thebibliography}{numbers of references}
 \bibitem{identifier}text \\
 \end{thebibliography}
```
### In your text you only need to use `\cite{identifier}` to link this text to the refrence you define above 
```bash
 \cite{identifier} --> links to 
 ```
### Standard method is (BibTex)
```bash
 \bibliographystyle{styles}
 \bibliography{bibfilename}
 ```
### Styles can be:
 - plain
 - abbrv
 - alpha
 - ieeetr
--------------------------------------------------------------------------------------------
## Formulas in Latex
### For using mathematical commands we need (AMS-Tex)
 ```bash
 \usepackage{amsmath}       --> # for mathematical typing
 \usepackage{mathtools}     --> # for mathematical typing 
 \usepackage{amssymb}       --> # for symbols in mathematics
 ```
 so:
 ```bash
 \usepackage{amsmath,amssymn}       --> #Generally we use this command at the begining
``` 
 ### Different types of mathematical relations:
 * inline: $...$
 * display:
 	* numbered: \equation
 	* unnumbered: \displaymath 
#### Example: 
```bash
 - According to Newton's law, the force to accelerate a mass $m$ with acceleration $a$, 
   is give by: $ F = m a$ (inline)
```
 OR:
 ```bash
 - According to Newton's law, the force to accelerate a mass $m$ with acceleration $a$, 
   is give by: 
   \begin{equation}
   	\label{eq.fma}
   	F=ma. (optional: \nonumber --> wont append number for formula)
   \end{equation} --> this will append formula in the center with number
   we will use equation eq. \ref{eq.fma} (eqref{eq.fma}) to calculate force.
```
### Fractions
```bash
 \frac{a}{b} \times \frec{b}{a} = \frac{a \times b}{b \times a} = 1
 \usepackage{xfrac} --> use extended form of fraction using for units.
 * Greek letters:
 \alpha     \beta    \gamma    \delta     \epsilon
 \Alpha     \Beta    \Gamma    \Delta     \Epsilon
 \phi
```
### Matrix 
```bash
 \begin{equation}
 	\mathbf{A} = \left{
 			\begin{array}{ccc}
 			  * & * & * \\
 			  * & * & * \\
 			  * & * & * \\
 			\end{array}
 		     \right}
 \end{equation}     
```
------------------------------------------------------------------------------------------		     
 
## Useful Mathematical Symbols and Commands:
 Note: Most of these commands are used within math environments `(e.g., $...$, $$...$$, \[...\], \begin{equation}...\end{equation})`.

### Basic Operations and Notation:
```bash
 \frac{numerator}{denominator} --> Creates a fraction.
 \sqrt[root]{expression} --> Generates a radical (square root if [root] is omitted).
 \binom{n}{k} --> Creates a binomial coefficient (n choose k).
 x_n --> Produces a subscript.
 x^n --> Produces a superscript.
 x^{y^z} --> Shows chained superscripts.
 \cdot --> Displays a multiplication dot (e.g., a⋅b).
 \times --> Displays a multiplication cross (e.g., a×b).
 \div --> Displays a division symbol (e.g., a÷b).
 \pm --> Plus-minus symbol (e.g., ±1).
 \mp --> Minus-plus symbol (e.g., ∓1).
```
### Grouping and Delimiters:
```bash
 \left( --> Automatically adjusts the size of an opening parenthesis.
 \right) --> Automatically adjusts the size of a closing parenthesis.
 \left[ --> Automatically adjusts the size of an opening square bracket.
 \right] --> Automatically adjusts the size of a closing square bracket.
 \left\{ --> Automatically adjusts the size of an opening curly brace.
 \right\} --> Automatically adjusts the size of a closing curly brace.
 \left| --> Automatically adjusts the size of a vertical bar (for absolute values, norms).
 \right| --> Automatically adjusts the size of a vertical bar.
 \left. --> Used when you need a matching \left but don't want a closing delimiter to appear (e.g., in piecewise functions).
 \right. --> Used when you need a matching \right but don't want an opening delimiter to appear.
```
### Ellipses and Dots:
```bash
 \cdots --> Center-aligned horizontal dots (e.g., 1+2+⋯+n).
 \vdots --> Vertical dots.
 \ldots --> Baseline horizontal dots (e.g., 1,2,…,n).
 \ddots --> Diagonal dots.
```
### Operators and Calculus:
```bash
 \sum_{lower}^{upper} expression --> Produces a summation. 
 \prod_{lower}^{upper} expression --> Produces a product.
 \int_{lower}^{upper} expression --> Produces an integral.
 \iint_{lower}^{upper} expression --> Produces a double integral.
 \iiint_{lower}^{upper} expression --> Produces a triple integral.
 \oint_{lower}^{upper} expression --> Produces a contour integral.
 \lim_{variable \to value} --> Produces a limit.
 \nabla --> Nabla operator (del operator) (e.g., ∇f).
 \partial --> Partial derivative symbol.
 \infty --> Infinity symbol (e.g., ∞).
```
### Accents and Diacritics:
```bash
 \hat{x} --> Puts a hat over the character.
 \tilde{x} --> Puts a tilde over the character.
 \bar{x} --> Puts a bar over the character.
 \vec{x} --> Puts a vector arrow over the character.
 \dot{x} --> Puts a dot over the character.
 \ddot{x} --> Puts two dots over the character.
```
### Relational Symbols:
```bash
 = --> Equals sign.
 \ne --> Not equal to.
 \approx --> Approximately equal to.
 \equiv --> Identical to or congruent.
 < --> Less than.
 > --> Greater than.
 \le --> Less than or equal to (≤).
 \ge --> Greater than or equal to (≥).
 \subset --> Subset of.
 \subseteq --> Subset or equal to.
 \supset --> Superset of.
 \supseteq --> Superset or equal to.
 \in --> Element of.
 \notin --> Not an element of.
 \sim --> Tilde (similar to).
```
### Arrows:
```bash
 \to --> Right arrow (e.g., A→B).
 \gets --> Left arrow (e.g., A←B).
 \leftrightarrow --> Left-right arrow.
 \Rightarrow --> Double right arrow (implies).
 \Leftarrow --> Double left arrow (is implied by).
 \Leftrightarrow --> Double left-right arrow (if and only if).
```
### Logic and Set Theory:
```bash
 \forall --> For all (universal quantifier).
 \exists --> There exists (existential quantifier).
 \land --> Logical AND (conjunction).
 \lor --> Logical OR (disjunction).
 \neg --> Logical NOT.
 \cup --> Set union.
 \cap --> Set intersection.
 \emptyset --> Empty set.
 \setminus --> Set difference.
```
### Miscellaneous Math Symbols:
```bash
 \degree --> Degree symbol
 \prime --> Prime symbol
 \aleph --> Aleph
 \Re --> Real part symbol.
 \Im --> Imaginary part symbol.
```
#### Remember to include the `amsmath` package in your preamble `(\usepackage{amsmath})` for many of these symbols and functionalities to work correctly.
```bash
 \mathrm{letter} --> print the symbol without italic form
 \mathbf{x} --> print x in the bold way to show its a vector
 \begin{align} ... \end{align}--> with &=
 \begin{subequations} ... \end{subequations} -->
 \numberwithin{equation}{section}
```
--------------------------------------------------------------------------------------------
# Usefull Latex Commands:
## General form of commands is:
```bash
 \command[option]{arg1}{arg2}...
```
## Document Information:
```bash
 \title{} --> Sets the title of your document.
 \author{} --> Specifies the author(s) of the document.
 \and --> Used to separate multiple authors within the \author command.
 \date{} --> Sets the date of the document.
 \date{\today} --> Automatically inserts the current date.
 \maketitle --> Generates the title, author, and date based on the above commands.
 ```
## Document Structure: 
```bash
 \begin{abstract} --> Marks the beginning of the abstract section.
 \end{abstract} --> Marks the end of the abstract section.
 \section{Section Name} --> Defines a major section in your document.
 \subsection{Subsection Name} --> Defines a subsection within a major section.
 \subsubsection{Subsubsection Name} --> Defines a sub-subsection within a subsection.
 \chapter{Chapter Name} --> (For book/report classes) Defines a new chapter.
 \part{Part Name} --> (For book/report classes) Defines a new part, grouping chapters.
 \label{name} --> Assigns a unique label to an element (like a section, figure, or table) for cross-referencing (e.g., \label{sec:introduction}).
 \ref{label_name} --> References the number of the labeled element (e.g., "See Section  \ref{sec:introduction}"). 
 \pageref{label_name} --> References the page number where the labeled element appears.
 \tableofcontents --> Generates a table of contents.
 \listoffigures --> Generates a list of figures.
 \listoftables --> Generates a list of tables.
```
## Text Formatting:
```bash
 \\ --> Inserts a line break.
 \newline --> Also inserts a line break.
 \par --> Starts a new paragraph.
 \texttt{Text} --> Displays text in monospace font (good for code, emails).
 \emph{Text} --> Emphasizes text (usually italics).
 \textrm{Text} --> Sets text in Roman font.
 \textsf{Text} --> Sets text in Sans-serif font.
 \textmd{Text} --> Sets text in medium series font.
 \textbf{Text} --> Sets text in bold font.
 \textit{Text} --> Sets text in italic font.
 \textsc{Text} --> Sets text in small capitals font.
 \textsl{Text} --> Sets text in slanted font.
 \verb"Text" --> Displays text verbatim, including special characters, often used for short code snippets.
 \TeX{} --> Displays the word "TeX" in its official logo style.
 \(special character) --> Used to escape special characters (e.g., \$ for a dollar sign).
 1\textsuperscript{st} --> Produces superscript text.
 Best\textsubscript{global} --> Produces subscript text. 
```
## Font Sizing:
```bash
 {\tiny text} --> Renders text in the tiniest available font size.
 {\scriptsize text} --> Renders text in a very small font size.
 {\footnotesize text} --> Renders text in a slightly small font size.
 {\small text} --> Renders text in a small font size.
 {\normalsize text} --> Renders text in the default normal font size.
 {\large text} --> Renders text in a large font size.
 {\Large text} --> Renders text in a larger font size.
 {\LARGE text} --> Renders text in an even larger font size.
 {\huge text} --> Renders text in a very huge font size.
 {\Huge text} --> Renders text in the largest available font size.
```
## Color:
```bash
 \textcolor{colorname}{Text} --> Changes the color of the specified text.
 {\color{colorname} Text} --> Changes the color of text within the group.
 \pagecolor{colorname} --> Sets the background color of the entire page (global).
 \colorbox{colorname}{Text} --> Puts text inside a colored box.
 \fcolorbox{framecolor}{bkcolor}{Text} --> Puts text inside a colored box with a colored frame.
```
## Spacing and Alignment:
```bash
 \\[xpt] --> Adds vertical space x points after the current line.
 \linespread{x} --> Globally changes the line spacing (use before \begin{document}).
 \hfill --> Inserts horizontal space that expands to fill the remaining line width, aligning text left and right on the same line (e.g., Left text \hfill Right text).
 \vfill --> Inserts vertical space that expands to fill the remaining page height, aligning content top and bottom.
 \noindent --> Prevents the first line of a paragraph from being indented.
 \indent --> Forces indentation of the first line of a paragraph.
 \hyphenation{words} --> A global command to prevent specific words from being hyphenated.
 \centering --> Centers content (e.g., \begin{center} ... \end{center}).
 \raggedright --> Aligns text to the left.
 \raggedleft --> Aligns text to the right.
```
## Custom Commands and Redefinitions:
 ```bash
 \newcommand{\CommandName}{command_definition} --> Defines a new command without arguments.
 \newcommand{\CommandName}[num_args]{command_definition} --> Defines a new command with num_args arguments.
 \renewcommand{\commandName}[num_args]{new_definition} --> Redefines an existing command.
```
## Footnotes and Endnotes:
```bash
\footnote{Footnote text} --> Adds a footnote at the bottom of the current page.
\endnote{Endnote text} --> Adds an endnote that will appear at the end of the document (requires \usepackage{endnote}).
\theendnotes --> Command to print all collected endnotes at a specific point in the document.
\thanks{Thanks text} --> Often used in author affiliations for acknowledgments or contact info (e.g., "Email: email@example.com").
```

## Text Decoration (Requires \usepackage{ulem}):
```bash
 \uline{Text} --> Underlines the text.
 \uuline{Text} --> Double underlines the text.
 \uwave{Text} --> Draws a wavy line under the text.
 \sout{Text} --> Strikes through the text with a single line.
 \xout{Text} --> Strikes through the text with an "x" pattern.
```
## Tables:
```bash
 \begin{tabular}{l c r} --> Creates a table environment with column alignment specified (l for left, c for center, r for right).
 \hline --> Draws a full horizontal line across the table.
 \cline{col1-col2} --> Draws a partial horizontal line from column col1 to col2.
 \multicolumn{num_cols}{alignment}{content} --> Merges num_cols columns into a single cell with specified alignment.
 \multirow{num_rows}{alignment}{content} --> Merges num_rows rows into a single cell (requires \usepackage{multirow}).
 \rowcolors{start_row}{odd_row_color}{even_row_color} --> Colors alternating rows in a table (requires \usepackage[table]{xcolor}).
 \cellcolor{colorname} --> Colors the background of a single cell.
```
## Images (Requires \usepackage{graphicx}):
```bash
 \DeclareGraphicsExtensions{.pdf,.png,.jpg} --> Declares the file extensions LaTeX should look for when including graphics.
 \includegraphics[options]{imagepath} --> Includes an image from the specified path with optional settings (e.g., [width=0.5\textwidth]).
 \begin{figure}[placement_options] and \end{figure} --> Environment for floating figures.
 \caption{Caption text} --> Adds a caption to a figure or table.
 \label{fig:name} --> Labels the figure for cross-referencing.
```
## Packages (Must be in your preamble, before \begin{document}):
```bash
 \usepackage{setspace} --> Provides commands for adjusting line spacing.
 \begin{doublespace}...\end{doublespace} --> Sets content within this environment to double spacing.
 \usepackage{microtype} --> Improves the overall typesetting by adjusting character spacing and line breaking.
 \usepackage{fixltx2e} --> Fixes some inconsistencies and potential bugs in older LaTeX versions.
 \usepackage{endnote} --> Enables the use of endnotes.
 \usepackage{ulem} --> Provides various text decoration commands like underlining and strikethrough.
 \usepackage{color} --> Provides basic color commands.
 \usepackage[table]{xcolor} --> Extends color capabilities, especially for tables.
 \usepackage{graphicx} --> Essential for including images.
 \usepackage{amsmath} --> Provides enhanced commands for mathematical typesetting.
 \usepackage{amsfonts} --> Provides additional mathematical fonts (e.g., blackboard bold).
 \usepackage{amssymb} --> Provides a large collection of mathematical symbols.
 \usepackage{enumerate} --> Customizes enumeration (numbered lists) environments.
 \usepackage{enumitem} --> More powerful and flexible list customization.
 \usepackage{hyperref} --> Creates clickable links in the PDF output for cross-references, URLs, etc.
 \usepackage{url} --> Formats URLs correctly.
```




























