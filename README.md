# Modern Lisp (ML): a Lisp-like language with ML semantics, user-definable ADTs, pattern matching, typed macros and delimited control

Had an internship at Ahrefs and loved OCaml.
```
(*
   a comment
   (* and just to be sure, this also stays a comment *)
*)

(* value definition *)
(let (x 1))
(let (x (fun -> 1))
(let (id (fun x -> x)))

(* function definition *)
(let (x -> 1)) (* thunk *) (* (x -> 1) in let : sugar for (x (fun -> 1)) *)
(let (id x -> x)) (* unary *) (* type (-> 'a 'a) *)
(let (id [x : int] -> x)) (* type (-> int int) *)
(* TODO: inlining the type of id up there: should it be a fully qualified type or just the value, if the params are given? *)

(* type definition *)
(type (foo int))

(* fun fact, you can stack definitions! *)
(let (x 1)
     (y (+ x x))
     (z (+ y y))
     (thunk -> z))

(* ditto for types *)
(type (* ^ represents products *)
      (unit (^))
      (pair (^ a b))
      (* | represents unions *)
      (peano (|
             (* ML will treat type constructors as values *)
             (* BTW we reserve capital letters for constructors *)
             (Z)
             (* keep in mind NOT (Z), thats a thunk *)
             (* here S is treated as a*)
             (S [x : peano])))
      (list a -> (|
                 (Nil)
                 (Cons [x : a] [xs: (list a)])))
      (bool (|
            (True)
            (False))))

(* letrec exists too *)
(letrec (even? n -> (match n
                    (Z -> True)
                    (S x -> (odd? x))))
        (odd? n -> (match n
                   (Z -> False)
                   (S x -> (even? x)))))
(* wut *)
(typerec (foo' (Foo [bar : bar']))
         (* can't forget datum comments too which are #* *)
         #*(invalid_code)
         (bar' (Bar [foo : foo'])))

(* TODO: mac and macrec *)

(* all of the defintions above are for toplevel DEFINITIONS, ie let/rec, type/rec, mac/rec. *)
(* for let/rec EXPRESSIONS, we use a slightly different syntax *)
(*
definition: (let (<pattern> <val>)+ ) (* only top level! *)
expression: (let (<pattern> <val>)+ -> <body> )
*)
(let (x 1) -> x)

(even? (S Z))
```
