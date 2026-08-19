---
title: Scheme Cheatsheet
description: A small cheatsheet for the Schme R5RS language
---

#cs #cheatsheet #scm #language

```lisp
; crappy scheme cheatsheet
; (c) 2026 fux

; constants
; numerical may be prefix with #e or #i for exact and inexact)

-23      ; integers
0.1      ; inexact real (float)
1/2      ; exact real
2.2+1.1i ; complex rectangular
1@0      ; complex polar

"hi"     ; string
#\a      ; char
#t       ; bool
#f       ; bool
'a       ; symbol
'(a b)   ; list (linked list)
#(1 2)   ; vector (array)

; prefixes to change base

#xFF   ; hex integer
#b1101 ; binary integer
#o7    ; octal integer

; working with lists

'()             ; empty list
'(a . b)        ; pair and improper list (last cdr isn't empty list)
'(a b)          ; proper list
'(a . (b . ())) ; same as above using dotted pair notation

(car '(a b c))   ; get car of list (a in this case)
(cdr '(a b c))   ; get "could-er" of list (in this case (b c))
(cons 'a '(b c)) ; link object to list, list becomes cdr
(list 1 2 3)     ; make list from given objects

; truthiness of values
; everything except #f is true

(if 'a #t #f) ; #t
(if 42 #t #f) ; #t

; procedure application
; first expression must be a procedure

(+ 1 1)
(cos 42)

; quoting 'e
; prevents evaluation of expression e
; helpful for lists and pairs

(quote x) ; long form
'x        ; becomes symbol
'(a . b)  ; becomes improper list

; sequence (begin e1 e2 ...)
; just evaluates all expressions from left to right
; returns value of last expression

(begin
  (+ 1 2)
  (* 3 4))

; numerical predicates (explains itself)
; these are the only predicates without a ? suffix

(< 1 2 3)
(> 1 2 3)
(<= 1 2 3)
(>= 1 2 3)
(= 1 2 3)

; conjunction (and e1 e2 ...)
; evaluates left to right
; returns first false object
; returns last true object if all true

(and '() '()) ; ()

; disjunction (or e1 e2 ...)
; evaluates left to right
; returns first true object
; returns last false object if all false

(or #f #f 42) ; 42

; negation (not e)
; explains itself

(not 20) ; false

; if (if t e1 e2)
; test expression t
; if t is true then evalutates epxression e1 otherwise e2
; e2 is optional
; other helpful conditionals are
; "case" https://index.scheme.org/filterset/r5rs/r5rs/case
; "cond" https://index.scheme.org/filterset/r5rs/r5rs/cond

(if (< 1 2) ; should return true lol
  "true"
  "false")

; local binding (let ((k v) ...) e1 e2 ...)
; bind variables to values
; existing bindinges get shadowed
; value of last expression is returned
; they're usually not core forms, they get expanded to lambda
; i.e. they're defined as extensions
; (let ((k v) ...) e1 e2 ...)
; ((lambda (k1 k2 ...) e1 e2 ...) v1 v2 ...)

(let ((x 21))
  (+ x x))

((lambda (x) (+ x x)) 21) ; same as above

(let ((x 1))   ; outer x gets shadowed (this returns 2)
  (let ((x 2))
    x))

; sequential local binding (let* ((v k) ...) e1 e2 ...)
; works like let but allows to reference vars from previous bindings
; bindings go from left to right

(let* ((x 2) (y (+ x 2))) ; 6
  (+ x y))

; assignment (set! x e)
; sets bound variable x to value of e
; all procedures with side effects have a ! suffix

(let ((x 2)) ; 4
  (set! x 4)
  x)

; lambda (lambda (args) e1 e2 ...)
; creates a procedure
; expressions in body are only executed on application
; value of last expression is returned

(lambda (p)     ; single parameter
  (+ p 1))

(lambda (p . v) ; single param + variable list
  (map p v))

(lambda v       ; variable list
  (map + v))

; definition (define x e)
; used for top-level definitions of variables

(define x 42)     ; binding to constant

(define f         ; binding to procedure
  (lambda (x)
    (+ x x)))

(define (f x)     ; short form lambda (same as above)
  (+ x x))

(define (f . v)   ; short form variable list
  (map + v))

(define (f p . v) ; short form param + variable list
  (map p v))

; closures
; procedures can capture the environment outside of their scope

(define counter
  (let ((static 0)) ; static gets captured
    (lambda ()
      (set! static (+ static 1))
      static)))

(counter) ; 1
(counter) ; 2

(define make-counter
  (lambda ()
    (let ((static 0)) ; static gets captured again
      (lambda ()
        (set! static (+ static 1))
        static))))

(define my-counter (make-counter))
(my-counter) ; 1
(my-counter) ; 2

; recursion
; everything is done via recursion as there are no looping constructs
; there's "do" but it's defined as a syntactic extension
; i.e. it's also just recursion under the hood
; to visualise the calls you can use "trace"

(define (length1 ls)             ; used length1 to not redefine length
  (if (null? ls)
      0                          ; base case
      (+ 1 (length1 (cdr ls))))) ; recursion step (not tail recursive!)

(trace length1)         
(length1 '(a b c))

; | > (length1 '(a b c))
; | | > (length1 '(b c))
; | | | > (length1 '(c))
; | | | | > (length1 '())
; | | | | 0
; | | | 1
; | | 2
; | 3
; 3 

; tail recursion
; to avoid a stack overflow, scheme supports tail recursion
; that way the last stack frame is just replaced
; for that to work the call needs to be the last returning expression
; this is called a "tail call"

(define (enumerate n)
  (if (= n 0)
      "done"
      (begin
        (display n)
        (newline)
        (enumerate (- n 1))))) ; tail call

(trace enumerate)
(enumerate 4)

; | > (enumerate 4)
; 4
; | > (enumerate 3)
; 3
; | > (enumerate 2)
; 2
; | > (enumerate 1)
; 1
; | > (enumerate 0)
; | "done"
; "done"

; named let (let f ((k v) ...) e1 e2 ...)
; this form lets you create a procedure with the let body and parameters

(define (length2 ls)
  (let f ((count 0) (ls ls)) ; behaves like a procedure with default values
    (if (null? ls)
        count
        (f (+ count 1) (cdr ls))))) ; tail call to f bound in let

; recursive let (letrec ((k v) ...) e1 e2 ...)
; defining recursive lambdas inside the bindings isn't possible in let
; because you run into the following issue with expansion
; (let ((f (lambda () (f)))) (f))
; becomes
; ((lambda (f) (f)) (lambda () (f)))
; as we know from the closures before
; variables are resolved using the environment where the lambda is created
; not where it is called!!!
; so f inside (lambda () (f)) looks for a top-level definition
; even if it is applied inside (lambda (f) (f))
; for that reason there exists letrec which avoids this problem

(letrec ((f (lambda (ls)     ; this does nothing, it's just for showcase
              (if (null? ls)
                  0
                  (f (cdr ls))))))
  (f '(a b c)))

; continuations



; syntactic extensions

; 9 8 7 6 5 4 3 2 1 0 1 2 3 4 5 6 7 8 99 8 7 6 5 4 3 2 1 0 1 2 3 4 5 6 7 8 9 ;


```