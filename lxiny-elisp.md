
# Table of Contents

1.  [Expressions, variables and functions](#org37f3013)
    1.  [Expressions](#orga3ee038)
    2.  [Variables](#org09cd35c)
    3.  [Functions](#org74ba278)
2.  [Buffers, windows and functions](#orgcd24f3a)
    1.  [Buffers](#orgf18d230)
    2.  [Let](#org3bf697f)
    3.  [String format](#orgdc1900f)
    4.  [Reading from the minibuffer](#org065446b)
3.  [Lists and functions](#orgbff9621)
    1.  [Lists](#org0a7f7f4)
    2.  [Search](#orgeac95c5)



<a id="org37f3013"></a>

# Expressions, variables and functions


<a id="orga3ee038"></a>

## Expressions

Elisp programma's bestaan uit symbolic expressions. Afgekort:
sexp. Ze worden omgeven met haakjes: ( en ).

    (+ 2 2)
    (* (+ 3 2) 3)


<a id="org09cd35c"></a>

## Variables

Met setq kun je een variabele (een naam die wijst naar een blok
geheugen) een waarde geven.

    (setq my-name "Arthur")


<a id="org74ba278"></a>

## Functions

Emacs heeft ingebouwde functies zoals insert. Hiermee kun je een
string in een buffer plaatsen op de locatie van point (Emacs jargon
voor cursor).

    (insert "Hello!")
    ;; more than one argument
    (insert "Hello" " world!")

Je kunt ook je eigen functies definiëren:

    (defun hello () (insert "Hello, I am " my-name))

De zojuist gedefinieerde functie kun je uitvoeren door z'n naam
binnen haakjes te plaatsen.

    (hello)

Een functie kun je argumenten meegeven.

    (defun hello (name) (insert "Hello " name))

De functie kun je als volgt aanroepen:

    (hello "you")


<a id="orgcd24f3a"></a>

# Buffers, windows and functions

In Emacs buffers are displayed in windows. The thing handled by your
window manager is called a frame in Emacs jargon.


<a id="orgf18d230"></a>

## Buffers

Je kunt een nieuwe buffer met de naam `*test*` openen in een nieuw
window.

    (switch-to-buffer-other-window "*test*")

Je kunt meerdere symbolic expressions combineren met "progn":

    (progn (switch-to-buffer-other-window "*test*")
           (hello "you"))

Een buffer legen kan met `erase-buffer`:

    (progn (switch-to-buffer-other-window "*test*")
           (erase-buffer)
           (hello "there"))

Terugkeren naar hetzelfde window is ook mogelijk:

    (progn
      (switch-to-buffer-other-window "*test*")
      (erase-buffer)
      (hello "you")
      (other-window 1)
      )


<a id="org3bf697f"></a>

## Let

Een lokale variabele kun je maken met een let binding.

    (let ((local-name "you"))
      (switch-to-buffer-other-window "*test*")
      (erase-buffer)
      (hello local-name)
      (other-window 1))


<a id="orgdc1900f"></a>

## String format

De opmaak van een string kun je bepalen met format:

    (format "Hello %s!\n" "visitor")

De string "visitor" wordt geplaatst op de locatie van `%s`.

We passen de functie hello aan:

    (defun hello (name)
      (insert (format "Hello %s!\n" name)))
    
    (hello "you")

Nog een functie met let:

    (defun greeting (name)
      (let ((your-name "Bastien"))
        (insert (format "Hello %s!\n\nI am %s."
    		    name
    		    your-name))))
    
    (greeting "you")


<a id="org065446b"></a>

## Reading from the minibuffer

Sommige functies zijn interactief. Je kunt bijvoorbeeld invoer
opnemen van de gebruiker.

    (read-from-minibuffer "Enter your name: ")

We passen de greeting functie aan om invoer op te nemen:

    (defun greeting (from-name)
      (let ((your-name (read-from-minibuffer "Enter your name: ")))
    	(insert (format "Hello!\n\nI am %s and your are %s."
    			from-name
    			your-name))))
    
    (greeting "Arthur")

We passen greeting nogmaals aan om het resultaat in een andere
buffer te laten zien:

    (defun greeting (from-name)
      (let ((your-name (read-from-minibuffer "Enter your name: ")))
        (switch-to-buffer-other-window "*test*")
        (erase-buffer)
        (insert (format "Hello!\n\nI am %s and your are %s." from-name your-name))
        (other-window 1)
        ))
    
    (greeting "test")


<a id="orgbff9621"></a>

# Lists and functions


<a id="org0a7f7f4"></a>

## Lists

Als je een "literal list" wil maken dan moet je hem voorzien van
een quote symbool. Doe je dit niet dan probeert de lisp interpreter
het eerste element uit te voeren alsof het een functie betrof.

    (setq list-of-names '("Sarah" "Chloe" "Mathilde"))
    
    (car list-of-names)
    
    (cdr list-of-names)

De functie car geeft het eerste element van de list als
resultaat. De functie cdr geeft als resultaat een list met alle
elementen behalve het eerste element.

Een element voeg je toe met `push`.

    (push "Stephanie" list-of-names)

Op ieder element van de list kun je een functie uitvoeren met
`mapcar`.

    (mapcar 'hello list-of-names)

We passen greeting weer aan:

    (defun greeting ()
      (switch-to-buffer-other-window "*test*")
      (erase-buffer)
      (mapcar 'hello list-of-names)
      (other-window 1)
      )
    
    (greeting)


<a id="orgeac95c5"></a>

## Search

In Emacs kun je zoeken naar tekst en het vervangen.

Een voorbeeld:

    (defun replace-hello-by-bonjour ()
      (switch-to-buffer-other-window "*test*")
      (goto-char (point-min))
      (while (search-forward "Hello")
        (replace-match "Bonjour"))
      (other-window 1)
      )
    
    (replace-hello-by-bonjour)

We passen `replace-hello-by-bonjour` aan, zodat hij geen foutmelding meer geeft.

    (defun replace-hello-by-bonjour ()
      (switch-to-buffer-other-window "*test*")
      (erase-buffer)
      (mapcar 'hello list-of-names)
      (goto-char (point-min))
      (while (search-forward "Hello" nil t)
        (replace-match "Bonjour"))
      (other-window 1)
      )
    
    (replace-hello-by-bonjour)

