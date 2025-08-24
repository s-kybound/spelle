# spelle: a Lisp-like language with ML semantics

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
(let (x (fun -> 1))) (* TODO: discuss whether (fun -> 1) is valid and produces
                        a Thunk (nullary function), or force minimum 1-ary
                        functions with (fun () -> 1) being recommended *)
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
  (thunk -> z))

(* ditto for types *)
(type
  (* ^ represents products *)
  (unit (^))
  (pair (^ a b))
  (* | represents unions *)
  (peano (|
           (* BTW we reserve capital letters for constructors *)
           Z
           (* keep in mind NOT (Z), thats a thunk *)
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
```
