# hops: a Lisp-like language with ML semantics

Had an internship at Ahrefs and loved OCaml for how it structures code and makes it easy for me to make programs

But I like Common Lisp and Scheme too, for their macros and homoiconic syntax. How do?

Hence I sought a way to unite the two mystical worlds of ML-type and Lisp-type languages together, and here we are! A lisp that tastes like ML, but also preserves the ease of macros and homoiconicity.
```lisp
(*
   a comment
   (* and just to be sure, this also stays a comment *)
*)

(* value definition *)
(let (x 1))
(let (x (fun () -> 1)))
(let (id (fun x -> x)))

(* function definition *)
(let (x -> 1)) (* thunk *) (* (x -> 1) in let : sugar for (x (fun -> 1)) *)
(let (id x -> x)) (* unary *) (* type (-> 'a 'a) *)
(let (id [x : int] -> x)) (* type (-> int int) *)
(* TODO: inlining the type of id up there: should it be a fully qualified type or just the value, if the params are given? *)

(* type definition *)
(type (foo int))

(* fun fact, you can stack definitions! *)
(let
  (x 1)
  (y (+ x x))
  (z (+ y y))
  (thunk () -> z))

(* ditto for types *)
(type
  (* ^ represents products *)
  (unit (^))
  (pair (^ a b))
  (* | represents unions *)
  (peano (|
           (* BTW we reserve capital letters for constructors *)
           Z
           (* here S is treated as a peano -> peano function *)
           (S [x : peano])))
  (list a -> (|
               Nil
               (Cons [x : a] [xs: (list a)])))
  (bool (|
          True
          False)))

(* letrec exists too *)
(letrec
  (even? n -> (match n
                (Z => True)
                ((S x) => (odd? x))))
  (odd? n -> (match n
               (Z => False)
               ((S x) => (even? x)))))
(* wut *)
(typerec
  (foo' (Foo [bar : bar']))
  (* can't forget datum comments too which are #* *)
  #*(invalid_code)
  (bar' (Bar [foo : foo'])))

(* TODO: mac and macrec *)

(* all of the defintions above are for toplevel DEFINITIONS, ie let/rec, type/rec, mac/rec. *)
(* for let/rec EXPRESSIONS, we use a slightly different syntax *)
(*
definition: (let (<pattern> <val>)+ ) (* only top level! *)
expression: (let (<pattern> <val>)+ => <body> )

(* we keep -> to represent functions and => to represent matches and progress *)
*)
(let (x 1) => x)

(even? (S Z))

(* unlike most lisps, arithmetic functions such as + are set as 2-ary, with type int -> int -> int *)
(+ 1 1) (* valid *)

#*(+ 1) (* -> (-> int int) *)
#*(+) (* -> (-> int (-> int int)) *) (* hmm, i suspect this means we also reevaluate that application (<term>) with no arguments is == <term>? ie useless when not given any arguments? *)
#*(+ 1 1 1 1) (* invalid *)

(* this allows us to use + as a curried function that can be passed to HOFs.
   but if we really want to get the original variadic +, this is where macros come in! *)

+... (* type: (-> (list (code int)) (code int)) *)

(+...) (* -> 0 *)
(+... 1) (* -> 1 *)
(+... 1 2 3) (* -> 6 *)

(* how does this black magic work? it converts (+... 1 2 3) into (+ 1 (+ 2 3)) (or some nice tail recursive version, up to implementor)! *)
```
