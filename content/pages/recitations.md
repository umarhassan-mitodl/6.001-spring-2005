---
content_type: page
description: This course is divided into recitation sections of about 30 to 35 students
  which meet twice per week. The purpose of the recitations is to expand upon course
  materials covered in lecture and the  supplementary materials and allow students
  to practice working with the material in an interactive setting. Recitations are
  the primary source of interaction with the staff. Below is a sampling of the material
  covered during recitations.
hide_download: true
hide_download_original: null
learning_resource_types:
- Recitations
ocw_type: CourseSection
title: Recitations
uid: 27bba2ec-5d35-c02e-e8e1-d54af27a948e
---

This course is divided into recitation sections of about 30 to 35 students which meet twice per week. The purpose of the recitations is to expand upon course materials covered in lecture and allow students to practice working with the material in an interactive setting. Below is material written by one of the recitation instructors for students enrolled in the course.

6.001 Recitation Notes, Spring 2005, by Recitation Instructor Peter Szolovits
-----------------------------------------------------------------------------

This Web page contains notes and program fragments that I have used in presenting some of the 6.001 recitations this term. A number of students have asked for these to be available, therefore I have collected them here.

*   Higher-order Procedures
*   "Alternative" Methods of Computing Square-root
*   Printing Pairs vs. Printing Lists
*   Reader, Evaluator and Printer
*   Maintaining a Priority Queue for Simulation

Higher-order Procedures
-----------------------

In the recitation following the introduction of higher-order procedures, I argued that there is a necessary sense of design elegance for building complex systems that remain comprehensible. One aspect of that elegance is related to regularity and the ability to compose operations. For example, we looked at two alternative implementations of how one might take a derivative of an arbitrary function of one variable:

The typical approach in ordinary programming languages is to define a procedure, say ddx, that takes as inputs the procedure that computes the function of one argument and the value, x, at which the derivative is to be evaluated.  
  
;; This is a small number that happens to have an exact binary fraction representation  
(define dx 0.00390625)

(define (ddx f x)  
  (/ (- (f (+ x dx))  
        (f x))  
     dx))  
  
We then invoke our procedure as follows:  
  
(ddx square 3)  
;Value: 6.00390625  
  
However, this procedure is, technically speaking, ugly! Its signature is  
  
((number ==> number), number) ==> number  
  
and it appears to confound two separate issues: (1) how to find a numerical approximation to the derivative of a function, and (2) how to evaluate that derivate at a particular value of x.  
  
Instead, we should prefer the following:  
  
(define (deriv f)  
  (lambda (x)  
    (/ (- (f (+ x dx))  
          (f x))  
       dx)))  
  
We use it as follows:  
  
((deriv square) 3)  
;Value: 6.00390625  
  
This deriv procedure's signature is  
  
(number ==> number) ==> (number ==> number)  
  
which is much more regular (it transforms an input of a certain type into an output of the same type), and then allows us to define ddx in terms of deriv, if we choose, as  
  
(define (ddx f x)  
  ((deriv f) x))  
  
Elegance has its rewards, because if we now become interested in taking the second derivative of a function, we can simply compose our existing ability to take the first derivative, twice. Thus, for example:  
  
((deriv (deriv square)) 3)  
;Value: 2.  
  
Further, we might try  
  
((deriv (deriv (deriv square))) 3)  
;Value: 0.  
  
which is great. In fact, if we define  
  
(define (compose f g)  
  (lambda (x)  
    (f (g x))))  
  
then note that we may define  
  
(define 2nd-deriv (compose deriv deriv))  
  
and indeed  
  
((2nd-deriv square) 8)  
;Value: 2.  
  
Note that we first saw compose as a procedure that composes two procedures each of which has signature  
  
number ==> number  
  
leading to examples of its use such as  
  
((compose square sqrt) 7)  
;Value: 7.000000000000001  
  
In the present case, we are using it on two procedures each of which has signature  
  
(number ==> number) ==> (number ==> number)  
  
The fact that we are able to do this without changing the definition of compose indicates that we have successfully captured some essence of the mathematical notion of composition.

"Alternative" Methods of Computing Square-root
----------------------------------------------

In lectures and the text, there are (at least) three fundamentally different presentations of how one might build a computer algorithm for computing the square root of a non-negative real number: (1) due to Heron of Alexandria, successively approximating y as the square root of x by averaging y and x/y, (2) a method that finds the fixed point of the transformation y ==> x/y, and (3) Newton's method, that finds a zero of the function y2 - x. Here are some implementations of each of these algorithms, and examples of their use. Note that each implementation traces successive values of the approximation, to let us see what is being computed.  
  
(define (average x y)  
  (/ (+ x y) 2))

(define tolerance 0.00001)

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;  
;;;  
;;; Heron of Alexandria's sqrt Method  
;;;  
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

(define (sqrt-heron x)

  (define (sqrt-iter guess x)  
    (display guess) (newline)  
    (if (good-enuf? guess x)  
 (improve guess x)  ;original returned guess; less accurate  
 (sqrt-iter (improve guess x)  
     x)))  
  (define (improve guess x)  
    (average guess (/ x guess)))

  (define (good-enuf? guess x)  
    (\< (abs (- (square guess) x))  
       tolerance))

  (sqrt-iter 1.0 x))

  
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;  
;;;  
;;; Fixed point method for sqrt  
;;;  
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

(define (average-damp f)  
  (lambda (x)  
    (average x (f x))))

(define (fixed-point f iguess)

  (define (close-enough? v1 v2)  
    (\< (abs (- v1 v2)) tolerance))

  (define (try guess)  
    (display guess) (newline)  
    (let ((next (f guess)))  
      (if (close-enough? guess next)  
   next  
   (try next))))

  (try iguess))

(define (fsqrt x)  
  (fixed-point  
   (average-damp  
    (lambda (y) (/ x y)))  
   1.0))

  
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;  
;;;  
;;; Newton's Method  
;;;  
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

(define dx tolerance)

(define (deriv f)  
  (lambda (x)  
    (/ (- (f (+ x dx))  
   (f x))  
       dx)))

(define (newton-transform g)  
  (lambda (x)  
    (- x (/ (g x)  
     ((deriv g) x)))))

(define (newtons-method g guess)  
  (fixed-point (newton-transform g) guess))

(define (nsqrt x)  
  (newtons-method  
   (lambda (y) (- (square y) x))  
   1.0))

  
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;  
;;;  
;;; Generalization (just for fun)  
;;;  
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

(define (fixed-point-of-transform g transform guess)  
  (fixed-point (transform g) guess))

(define (fsqrt1 x)  
  (fixed-point-of-transform  
   (lambda (y) (/ x y))  
   average-damp  
   1.0))

(define (nsqrt1 x)  
  (fixed-point-of-transform  
   (lambda (y) (- (square y) x))  
   newton-transform  
   1.0))  
  
Using these definitions, we can compute (sqrt 2) in the three different ways:  
  
(sqrt-heron 2)  
1.  
1.5  
1.4166666666666665  
1.4142156862745097  
;Value: 1.4142135623746899

(fsqrt 2)  
1.  
1.5  
1.4166666666666665  
1.4142156862745097  
;Value: 1.4142135623746899

(nsqrt 2)  
1.  
1.4999975000090175  
1.416666805550865  
1.4142156951657834  
;Value: 1.4142135623822438  
  
Note that the first two methods are really doing precisely the same calculation. The third differs only in that in Newton's method we are really just approximating the derivative, rather than computing it directly. Even so, the third method also goes through the same number of steps of calculation and intermediate values to arrive at an answer that differs from the first two only in the 12th significant digit! Thus, in essence, each of these three methods is actually doing exactly the same thing.  
  
Note also that each method converges quickly even for much larger arguments:  
  
(fsqrt 2000)  
1.  
1000.5  
501.24950024987504  
252.6197645828103  
130.26840074561815  
72.81065908632895  
50.13958236477863  
45.014113668459046  
44.72231152890744  
44.721359560127915  
;Value: 44.721359549995796

(nsqrt 2000)  
1.  
1000.49500282873  
501.2470075904563  
252.61852978302747  
130.26780512293146  
72.81039859738789  
50.13950281939877  
45.01410604971975  
44.722311512074825  
44.721359560234205  
;Value: 44.721359549995796

Printing Pairs vs Printing Lists
--------------------------------

Scheme generally prints a pair in the following form: (\<car> . \<cdr>)  
  
Therefore, assuming we have the function write that can print atoms and character strings, we may write a procedure for printing structures made of pairs as follows:  
  
(define (my-write e)  
  (cond ((pair? e)  
         (display "(")  
         (my-write (car e))  
         (display " . ")  
         (my-write (cdr e))  
         (display ")"))  
        (else (write e))))  
  
Then, we get the following:  
  
(my-write (cons (cons 1 2)  
                (cons 3 (cons 4 5))))  
((1 . 2) . (3 . (4 . 5)))  
  
Note, however, that this method of printing makes lists look rather more complex than we ordinarily like to think of a list of objects:  
  
(my-write (list 1 2 3 4))  
(1 . (2 . (3 . (4 . #f))))  
  
To get around this, Scheme in fact uses a printer more like the following, which is haired up to deal better with lists:  
  
(define (your-write e)  
  (define (list-write e)  
    (cond ((pair? e)  
           (your-write (car e))  
           (display " ")  
           (list-write (cdr e)))  
          ((null? e))  
          (else (display ". ")  
                (write e))))  
  (cond ((pair? e) (display "(")  
                   (list-write e)  
                   (display ")"))  
        (else (write e))))  
  
Now, as desired, we get  
  
(your-write  
     (cons 1 (cons 2 (cons 3 '()))))  
(1 2 3 )

(your-write  
     (cons (cons 1 2) (cons 3 (cons 4 5))))  
((1 . 2) 3 4 . 5)  
  
In fact, with the exception of an extra space before the end of a list, this is just like what Scheme's built-in printer does. You can verify that this algorithm is equivalent to the "hack" I suggested in section: print using the first method (fully dotted and parenthesized), and then erase all occurrences of ". (" and the corresponding ")". (This also assumes that the empty list is printed as () rather than as #f.)

Reader, Evaluator, Printer
--------------------------

You have seen before the Scheme's interaction with the user involves three interlinked functionalities:

*   The reader turns the user's typed input into the proper internal representations of numbers, character strings, symbols, Boolean value, lists, etc.
*   The evaluator applies Scheme's rules of evaluation to find the value of an expression that has been entered by the user.
*   The printer turns the internal representation back into a meaningful form for the user.

For a simple example, suppose the user types the characters "3.14159265358979323846" into Scheme's top-level REPL (read./eval/print loop). The reader recognizes that this sequence of characters is a valid syntactic representation of a number, and constructs the floating point internal representation of (an approximation) of the numbers the user meant to type. Scheme's evaluator then applies it normal rules of evaluation; it discovers that the expression is a number, which is self-evaluating, and therefore returns the number as its value. The printer then converts this internal representation of the number to a form that is presented to the user. Here is a transcript of this interaction:  
  
3.14159265358979323846  
;Value: 3.141592653589793  
  
Note that what got typed back is not exactly what got typed in. This is a result of limitations in the internal representations used by Scheme. In this example, the IEEE floating point standard used by Scheme does not support as many significant digits of precision as the user typed in.  
  
The reader understands the purely syntactic rules of Scheme, and can determine whether what the user typed is meant to be a number (as in the above example) or a Boolean value (#t or #f) or a character string ("I am inside double-quote marks".) or a symbol (e.g., + or square). It also knows the syntactic rule for quotation:  
  
'\<anything>  
  
is read as  
  
(quote \<anything>)  
  
and it understands the syntax of list structure and dotted-pair notation. Thus,  
  
(+ 2 3)  
  
is read as a list of three elements, the symbol +, the number 2 and the number 3. Note, however, that the reader knows nothing about the rules of evaluation, and thus cares not at all whether the user has typed (quote 18) or (sqrt 18).  
  
The reader's rules are generally intuitive, and never ambiguous. However, you might find something like the following odd:  
  
3.1.3  
;Unbound variable: 3.1.3  
  
This is because a number cannot have two periods, so the reader reads "3.1.3" as a symbol. The evaluator then finds that there is no value associated with this symbol.

Quotation, Lists and the Reader
-------------------------------

We have recently introduced quotation as a means of allowing our programs to work with symbols and also as a convenient way to type lists into scheme. For example, we can try  
  
(define colors '(red orange yellow green blue indigo violet))  
(member? 'orange colors)  
  
For the first line, the reader produces the expression  
  
(define colors (quote (red orange yellow green blue indigo violet)))  
  
The evaluator then applies the rule of evaluation for the special form define, which says to evaluate the second argument to define and then associate the symbol that is the first argument with the resulting value. The value of (quote (red orange yellow green blue indigo violet)) is just (red orange yellow green blue indigo violet) by the rule for evaluating the special form quote.  
  
From these examples, you can actually tell a profound fact about the implementation of Scheme: the expressions being evaluated by the evaluator are represented internally as list structure. This is why typing in a list that is quoted leads to a value that is indeed a list. And you have the reader to thank for having created it when it recognized that (roughly), stuff surrounded by parentheses is meant to represent a list.

Maintaining a Priority Queue for Simulation
-------------------------------------------

Having introduced mutation of data structures, we looked in recitation at how to implement a useful data structure to keeping a set of elements ordered by some "priority" rank. This is used, for example, in building (discrete event) simulators, where one needs to keep track of tasks that must be done in the future, and the times at which they need to be started. Here is the priority queue representation and implementation we developed:  
  
(define make-task cons)  
(define task-time car)  
(define task-proc cdr)

(define (make-task-list)  
  ;; \[\] -> task-list  
  (list 'task-list)  
  )

(define (add-task! tl task)  
  ;; \[task-list, task\] -> ???  
  (define (helper trail l)  
    (cond ((null? l) (set-cdr! trail (list task)))  
   ((\< (task-time task) (task-time (car l)))  
    (set-cdr! trail (cons task l)))  
   (else (helper l (cdr l)))))  
  (helper tl (cdr tl))  
  tl  
  )

(define (task-list? obj)  
  (and (pair? obj)  
       (eq? (car obj) 'task-list)))

(define (empty-task-list? tl)  
  ;; task-list -> boolean  
  (and (task-list? tl)  
       (null? (cdr tl)))  
  )

(define (pop-task! tl)  
  ;; task-list -> task  
  (and (task-list? tl)  
       (not (empty-task-list? tl))  
       (let ((ans (cadr tl)))  
  (set-cdr! tl (cddr tl))  
  ans)))  
  
A typical task is represented as a procedure of no arguments. The idea is that it will be called when it is time to perform the task. In a simulator, usually tasks will, as part of their execution, add other tasks to the task-list, to be done at later times.  
  
(define (say msg)  
  (lambda ()  
    (newline)  
    (display msg)))

With the above, we can implement a simple simulation system that will "ping" every five ticks of the clock and "pong" every seven ticks. Note that our simulator, run, does not actually have a ticking clock, but merely executes the next available task from the task list. The clock is, therefore, kept implicitly by the (future) times for which each task queues up other tasks.  
  
(define (run tl)  
  (cond ((empty-task-list? tl)  
  'done)  
 (else  
  (let ((task (pop-task! tl)))  
    ((task-proc task)))  
  (run tl))))

(define the-task-list (make-task-list))

(define (ping initial-time msg)  
  (add-task!  
   the-task-list  
   (make-task initial-time  
       (lambda ()  
  (newline)  
  (display msg)  
  (ping (+ 5 initial-time)  
        (list 'ping (+ 5 initial-time)))))))

(define (pong initial-time msg)  
  (add-task!  
   the-task-list  
   (make-task initial-time  
       (lambda ()  
  (newline)  
  (display msg)  
  (pong (+ 7 initial-time)  
        (list 'pong (+ 7 initial-time)))))))

(ping 0 "PING Starting up")  
(pong 0 "PONG Starting up")

(run the-task-list)

PING Starting up  
PONG Starting up  
(ping 5)  
(pong 7)  
(ping 10)  
(pong 14)  
(ping 15)  
(ping 20)  
(pong 21)  
(ping 25)  
(pong 28)  
(ping 30)  
(pong 35)  
(ping 35)  
(ping 40)  
(pong 42)  
(ping 45)  
(pong 49)  
(ping 50)  
(ping 55)  
(pong 56)  
...