# posh-syntax
POSH (Parts of Speech Heuristics) Syntax Language Reference

* Status: DRAFT 
* Version: 0.1
* Authored by the POSH Workgroup 
	* Chair: brianhray@gmail.com
* URL: https://github.com/brianray/posh-syntax


# Introduction

POSH is a micro-language for writing POS (Parts of Speach) Rules. 

Example Hello World:

	SENT: NN(hello) & NN(world) = true

This will parse the sentence:

	hello world!
	
This would return: true

Reserved key words:

* SENT
* PARA
* true
* false

## Definition

BNF (Backus–Naur Form) grammar notation.

	 <posh-rule> ::= <scope>: <exprs> = <returns> <EOL>
	 <exprs>     ::= <exper> | <exper> <oper> <exper>
	 <exper>     ::= <class>(<word>)
	 <oper>      ::= '&' | '|'
	 <scope>     ::= 'SENT' | 'PARA' | 'SECTION' | 'DOC'
	 <word>      ::=  {word} ['+' <hyper>] ['-' <hypo>]
	 <returns>   ::= {return} | {return}, {return}
	 <return>    ::= 'matchs' | 'true' | 'false' | {float} | {int}  
	 <flags>     ::= [<flag>, <flag>] or <flag>
	 <flag>      ::= {flag}
	 
Key:

* posh-rule: a complete rule
* scope: 
	* SENT: look in the scope of each sentence. All enclosed conditions must be true
	* PARA: Look in the scope of a paragraph
	* SECTION: look at larger section deliminated by some title format
	* DOC: look in the schope of all given text for that subject
* exprs: abstract 'Expression' contaiing one 'exper' (expression), or a chain held together with oper (operator)
* class: The word class provided by user given taxonomy
* word: the string that represents the word
	* hyper: the max distance above in a parse tree
	* hypo: the max distance below in a parse tree 
* oper: operator that sits between expressions for truth checking
* return: what to reply back to caller with if all cases are true
	* 'true' or 'false' returns booleans
	* matchs *see match return format[^1]*
* flags: comma list of flags [platform independent]for the handler. For example:
	* 'i' could mean case*i*nsensative
	* 'g' could mean *g*reedy 


[^1] Match return format:

	['exper', #, word, found at possition]
	['exper', #, word, found at possition]
	...


## Examples

Rule:

    SENT: NN(noise+3) = true
	
Example Text:

	'The sound in the place is terrible.'
	
Returns:

	true

----

Rule:

    SENT: NN(noise+3) = matchs
	
Example Text:

	'dining with clatter and the occasional smell of BMW exausts'	
Returns:

	['NN(noise)', 1, 'clatter',  12]

----

Rule:

    SENT: NNS(acoustics) & RB(not) = matchs
	
Example Text:

	'Also, the acoustics are not conducive to having any sort of conversation.'	
Returns:

	['NN(acoustics)', 1, 'acoustics',  10]
	['RB(not)', 1, 'not',  25]	
	

----


Rule:

    SENT: VB*(noise+3) = false
	
Example Text:

	'not to sound like a snob'
	
Returns:

	false


----

Rule:

    SENT: JJ(noise+3) = matches
	
Example Text:

	'at your table and you can tune the sound to whichever game you're interested in. That is cool'
	
Returns:

	['JJ(noise+3)', 1, 'sound',  35]	

----

Rule:

    SENT: VB*(noise+3) = false
	
Example Text:

	'oh god I sound old!'
	
Returns:

	false


----

Rule:

    SENT: NN(noise-1) = matches
	
Example Text:

	'my heart goes boom boom boom'
	
Returns:

	['NN(noise-1)', 1, 'boom',  15]	
	['NN(noise-1)', 2, 'boom',  21]	
	['NN(noise-1)', 3, 'boom',  27]	

















