###Propositional Temporal Logics



![image-20201218171640098](LTL证明5.assets\image-20201218171640098.png)

#### pvs描述

```
 LTL : nonempty_type = boolean


    p,q,r         : var bool
    l,l1,l2       : var LTL

    X  			  : [LTL -> LTL]
    U   		  : [LTL , LTL -> LTL]

    F(l)		  : LTL = U(true,l)
    R(l1,l2)	  : LTL = not (U(not l1 , not l2))
    G(l)		  : LTL = R(false , l)
    W(l1,l2)	  : LTL = U(l1,l2) or G(l1)
```



```
%%% Future Axiom
    F0        :   axiom G(l) => l
    F1	      :   axiom X(not l) <=> not(X(l))
    F3	      :   axiom G(l1 implies l2) => G(l1) implies G(l2)
    F2	      :   axiom X(l1 implies l2) => X(l1) implies X(l2)
    F4	      :   axiom G(l) => G(X(l))
    F5	      :   axiom (l implies X(l)) => (l implies G(l))
    F6	      :   axiom U(l1,l2) <=> (l2 or l1 and X(U(l1,l2)))
    F7        :   axiom U(l1,l2) => F(l2)
```



```
  %%%Inference Rules
    INS       :  lemma  l => G(l)
    MP	      :  lemma  G(l1 => l2) and Gl1 => Gl2
    					SQT()
    					SQT(l1 => l2 and l1  ,  l2) 
    SQT(a,b)   :	 G(a) => G(b)
```



```
 %%%Theorems and Rules of The Deductive System

	%%%Some useful lemmas
	
    PAR      :  lemma G(l) => l
    E_MP     :  lemma ((l1 and l2) => l) and G(l1) and G(l2) => G(l)  %%l1 l2 l3 ... ln  如何扩展
    E_MP_01  :	lemma (l1 => l) and G(l1) => G(l)

    E_TRNS   :  lemma (l1 => l2) and (l2 => l) => (l1 => l) %% l1 => l2  等价于  □l1 -> l2
    R1	     : 	lemma G(l2) => (l1 => l2)
    R2	     :	lemma (l1 => l2) => (not l2 => not l1)
    R3	     : 	lemma (l1 <=> l2) => ((l1 => l2) and (l2 => l1))
    R4	     :	lemma (l1 => l) and (l2 => l) => (l1 and l2 => l)
    R5	     :	lemma (l => l1) and (l => l2) => (l => l1 and l2)
    R6	     :	lemma (l1 => l2) and (l2 => l1) => (l1 <=> l2)
    R7	     :	lemma (l1 => (l2 => l)) => (l1 and l2) => l


	%%%Some axioms to prove
	
    %%% computational induction
    Rci	     :	lemma (l => X(l)) => (l => G(l))
    
    %%% X generalization
    RXgen    :	lemma G(l) => G(X(l))
    %%% X is monotonic
    RXmon    :	lemma (l1 => l2) => (X(l1) => X(l2))

    T3	     :  theorem	  X(l1 and l2) <=> X(l1) and X(l2)
    T4	     :	theorem	  G(l) => X(G(l))   

    %%% G generalization
    RGgen    :	lemma G(l) => G(G(l))
    %%% G is monotonic
    RGmon    :	lemma (l1 => l2) => (G(l1) => G(l2))

    T5	     :	theorem	  G(l1) or G(l2) => G(l1 or l2)
    T6	     :	theorem	  G(l1 and l2) <=>  G(l1) and G(l2)
```



####pvs证明步骤



##### R1  

![image-20201218171647749](LTL证明5.assets\image-20201218171647749.png)

```
R1 :  

  |-------
{1}   FORALL (l1, l2: LTL): G(l2) => (l1 => l2)

Rule? (skolem!)
Skolemizing,
this simplifies to: 
R1 :  

  |-------
{1}   G(l2!1) => (l1!1 => l2!1)

Rule? (case "l2!1 => (l1!1 => l2!1)")
Case splitting on 
   l2!1 => (l1!1 => l2!1), 
this yields  2 subgoals: 
R1.1 :  

{-1}  l2!1 => (l1!1 => l2!1)
  |-------
[1]   G(l2!1) => (l1!1 => l2!1)

Rule? (lemma E_MP_01)
Applying E_MP_01 
this simplifies to: 
R1.1 :  

{-1}  FORALL (l, l1: LTL): (l1 => l) AND G(l1) => G(l)
[-2]  l2!1 => (l1!1 => l2!1)
  |-------
[1]   G(l2!1) => (l1!1 => l2!1)

Rule? (inst -1  "l1!1 => l2!1" "l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 => l2!1, l2!1,
this simplifies to: 
R1.1 :  

{-1}  (l2!1 => l1!1 => l2!1) AND G(l2!1) => G(l1!1 => l2!1)
[-2]  l2!1 => (l1!1 => l2!1)
  |-------
[1]   G(l2!1) => (l1!1 => l2!1)

Rule? (lemma F0)
Applying F0 
this simplifies to: 
R1.1 :  

{-1}  FORALL (l: LTL): G(l) => l
[-2]  (l2!1 => l1!1 => l2!1) AND G(l2!1) => G(l1!1 => l2!1)
[-3]  l2!1 => (l1!1 => l2!1)
  |-------
[1]   G(l2!1) => (l1!1 => l2!1)

Rule? (inst -1 "l1!1 => l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 => l2!1,
this simplifies to: 
R1.1 :  

{-1}  G(l1!1 => l2!1) => l1!1 => l2!1
[-2]  (l2!1 => l1!1 => l2!1) AND G(l2!1) => G(l1!1 => l2!1)
[-3]  l2!1 => (l1!1 => l2!1)
  |-------
[1]   G(l2!1) => (l1!1 => l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of R1.1.

R1.2 :  

  |-------
{1}   l2!1 => (l1!1 => l2!1)
[2]   G(l2!1) => (l1!1 => l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of R1.2.

Q.E.D.
```



#####Rci

![image-20201218171656124](LTL证明5.assets\image-20201218171656124.png)

```
Rci :  

  |-------
{1}   FORALL (l: LTL): (l => X(l)) => (l => G(l))

Rule? (skolem!)
Skolemizing,
this simplifies to: 
Rci :  

  |-------
{1}   (l!1 => X(l!1)) => (l!1 => G(l!1))

Rule? (lemma F5)
Applying F5 
this simplifies to: 
Rci :  

{-1}  FORALL (l: LTL): (l IMPLIES X(l)) => (l IMPLIES G(l))
  |-------
[1]   (l!1 => X(l!1)) => (l!1 => G(l!1))

Rule? (inst?)
Found substitution:
l: LTL gets l!1,
Using template: G(l)
Instantiating quantified variables,
Q.E.D.
```



#####RXgen

![image-20201218171702845](LTL证明5.assets\image-20201218171702845.png)

```
RXgen :  

  |-------
{1}   FORALL (l: LTL): G(l) => G(X(l))

Rule? (skolem!)
Skolemizing,
this simplifies to: 
RXgen :  

  |-------
{1}   G(l!1) => G(X(l!1))

Rule? (lemma F4)
Applying F4 
this simplifies to: 
RXgen :  

{-1}  FORALL (l: LTL): G(l) => G(X(l))
  |-------
[1]   G(l!1) => G(X(l!1))

Rule? (inst?)
Found substitution:
l: LTL gets l!1,
Using template: G(X(l))
Instantiating quantified variables,
Q.E.D.



RXgen :  

  |-------
{1}   FORALL (l: LTL): G(l) => G(X(l))

Rule? (use F4)
Using lemma F4,
Q.E.D.
```



##### ==RXmon==

![image-20201218171709883](LTL证明5.assets\image-20201218171709883.png)

```
RXmon :  

  |-------
{1}   FORALL (l1, l2: LTL): (l1 => l2) => (X(l1) => X(l2))

Rule? (skolem!)
Skolemizing,
this simplifies to: 
RXmon :  

  |-------
{1}   (l1!1 => l2!1) => (X(l1!1) => X(l2!1))

Rule? (flatten)
Applying disjunctive simplification to flatten sequent,
this simplifies to: 
RXmon :  

{-1}  (l1!1 => l2!1)
{-2}  X(l1!1)
  |-------
{1}   X(l2!1)

Rule? (lemma INS)
Applying INS 
this simplifies to: 
RXmon :  

{-1}  FORALL (l: LTL): l => G(l)
[-2]  (l1!1 => l2!1)
[-3]  X(l1!1)
  |-------
[1]   X(l2!1)

Rule? (inst -1 "l1!1 => l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 => l2!1,
this simplifies to: 
RXmon :  

{-1}  (l1!1 => l2!1) => G(l1!1 => l2!1)
[-2]  (l1!1 => l2!1)
[-3]  X(l1!1)
  |-------
[1]   X(l2!1)

Rule? (lemma RXgen)
Applying RXgen 
this simplifies to: 
RXmon :  

{-1}  FORALL (l: LTL): G(l) => G(X(l))
[-2]  (l1!1 => l2!1) => G(l1!1 => l2!1)
[-3]  (l1!1 => l2!1)
[-4]  X(l1!1)
  |-------
[1]   X(l2!1)

Rule? (inst -1 "l1!1 => l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 => l2!1,
this simplifies to: 
RXmon :  

{-1}  G(l1!1 => l2!1) => G(X(l1!1 => l2!1))
[-2]  (l1!1 => l2!1) => G(l1!1 => l2!1)
[-3]  (l1!1 => l2!1)
[-4]  X(l1!1)
  |-------
[1]   X(l2!1)

Rule? (lemma F2)
Applying F2 
this simplifies to: 
RXmon :  

{-1}  FORALL (l1, l2: LTL): X(l1 IMPLIES l2) => X(l1) IMPLIES X(l2)
[-2]  G(l1!1 => l2!1) => G(X(l1!1 => l2!1))
[-3]  (l1!1 => l2!1) => G(l1!1 => l2!1)
[-4]  (l1!1 => l2!1)
[-5]  X(l1!1)
  |-------
[1]   X(l2!1)

Rule? (inst -1 l1!1 l2!1)
Instantiating the top quantifier in -1 with the terms: 
 l1!1, l2!1,
this simplifies to: 
RXmon :  

{-1}  X(l1!1 IMPLIES l2!1) => X(l1!1) IMPLIES X(l2!1)
[-2]  G(l1!1 => l2!1) => G(X(l1!1 => l2!1))
[-3]  (l1!1 => l2!1) => G(l1!1 => l2!1)
[-4]  (l1!1 => l2!1)
[-5]  X(l1!1)
  |-------
[1]   X(l2!1)

Rule? (lemma F0)
Applying F0 
this simplifies to: 
RXmon :  

{-1}  FORALL (l: LTL): G(l) => l
[-2]  X(l1!1 IMPLIES l2!1) => X(l1!1) IMPLIES X(l2!1)
[-3]  G(l1!1 => l2!1) => G(X(l1!1 => l2!1))
[-4]  (l1!1 => l2!1) => G(l1!1 => l2!1)
[-5]  (l1!1 => l2!1)
[-6]  X(l1!1)
  |-------
[1]   X(l2!1)

Rule? (inst -1 "X(l1!1 => l2!1)")
Instantiating the top quantifier in -1 with the terms: 
 X(l1!1 => l2!1),
this simplifies to: 
RXmon :  

{-1}  G(X(l1!1 => l2!1)) => X(l1!1 => l2!1)
[-2]  X(l1!1 IMPLIES l2!1) => X(l1!1) IMPLIES X(l2!1)
[-3]  G(l1!1 => l2!1) => G(X(l1!1 => l2!1))
[-4]  (l1!1 => l2!1) => G(l1!1 => l2!1)
[-5]  (l1!1 => l2!1)
[-6]  X(l1!1)
  |-------
[1]   X(l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,
Q.E.D.
```



##### ==T3==

![image-20201218171721476](LTL证明5.assets\image-20201218171721476.png)

```
T3 :  

  |-------
{1}   FORALL (l1, l2: LTL): X(l1 AND l2) <=> X(l1) AND X(l2)

Rule? (skolem!)
Skolemizing,
this simplifies to: 
T3 :  

  |-------
{1}   X(l1!1 AND l2!1) <=> X(l1!1) AND X(l2!1)

Rule? (split 1)
Splitting conjunctions,
this yields  2 subgoals: 
T3.1 :  

  |-------
{1}   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (postpone)
Postponing T3.1.

T3.2 :  

  |-------
{1}   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (case "l1!1 => (l2!1 => l1!1 and l2!1)")
Case splitting on 
   l1!1 => (l2!1 => l1!1 AND l2!1), 
this yields  2 subgoals: 
T3.2.1 :  

{-1}  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule?  (lemma RXmon)
Applying RXmon 
this simplifies to: 
T3.2.1 :  

{-1}  FORALL (l1, l2: LTL): (l1 => l2) => (X(l1) => X(l2))
[-2]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule?  (inst -1 "l1!1" "l2!1 => l1!1 AND l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1, l2!1 => l1!1 AND l2!1,
this simplifies to: 
T3.2.1 :  

{-1}  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (X(l1!1) => X(l2!1 => l1!1 AND l2!1))
[-2]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (lemma F2)
Applying F2 
this simplifies to: 
T3.2.1 :  

{-1}  FORALL (l1, l2: LTL): X(l1 IMPLIES l2) => X(l1) IMPLIES X(l2)
[-2]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (X(l1!1) => X(l2!1 => l1!1 AND l2!1))
[-3]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (inst -1 "l2!1" "l1!1 AND l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l2!1, l1!1 AND l2!1,
this simplifies to: 
T3.2.1 :  

{-1}  X(l2!1 IMPLIES l1!1 AND l2!1) => X(l2!1) IMPLIES X(l1!1 AND l2!1)
[-2]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (X(l1!1) => X(l2!1 => l1!1 AND l2!1))
[-3]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (lemma E_TRNS)
Applying E_TRNS 
this simplifies to: 
T3.2.1 :  

{-1}  FORALL (l, l1, l2: LTL): (l1 => l2) AND (l2 => l) => (l1 => l)
[-2]  X(l2!1 IMPLIES l1!1 AND l2!1) => X(l2!1) IMPLIES X(l1!1 AND l2!1)
[-3]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (X(l1!1) => X(l2!1 => l1!1 AND l2!1))
[-4]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (inst -1 "X(l1!1)" " X(l2!1 => l1!1 AND l2!1)"
                 " X(l1!1) IMPLIES X(l1!1 AND l2!1)")
Instantiating the top quantifier in -1 with the terms: 
 X(l1!1),  X(l2!1 => l1!1 AND l2!1),  X(l1!1) IMPLIES X(l1!1 AND l2!1),
this simplifies to: 
T3.2.1 :  

{-1}  (X(l2!1 => l1!1 AND l2!1) => X(l1!1) IMPLIES X(l1!1 AND l2!1)) AND
       ((X(l1!1) IMPLIES X(l1!1 AND l2!1)) => X(l1!1))
       => (X(l2!1 => l1!1 AND l2!1) => X(l1!1))
[-2]  X(l2!1 IMPLIES l1!1 AND l2!1) => X(l2!1) IMPLIES X(l1!1 AND l2!1)
[-3]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (X(l1!1) => X(l2!1 => l1!1 AND l2!1))
[-4]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (lemma R7)
Applying R7 
this simplifies to: 
T3.2.1 :  

{-1}  FORALL (l, l1, l2: LTL): (l1 => (l2 => l)) => (l1 AND l2) => l
[-2]  (X(l2!1 => l1!1 AND l2!1) => X(l1!1) IMPLIES X(l1!1 AND l2!1)) AND
       ((X(l1!1) IMPLIES X(l1!1 AND l2!1)) => X(l1!1))
       => (X(l2!1 => l1!1 AND l2!1) => X(l1!1))
[-3]  X(l2!1 IMPLIES l1!1 AND l2!1) => X(l2!1) IMPLIES X(l1!1 AND l2!1)
[-4]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (X(l1!1) => X(l2!1 => l1!1 AND l2!1))
[-5]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (inst -1 "X(l1!1)" "X(l2!1)" "X(l1!1) and X(l2!1)")
Instantiating the top quantifier in -1 with the terms: 
 X(l1!1), X(l2!1), X(l1!1) and X(l2!1),
this simplifies to: 
T3.2.1 :  

{-1}  (X(l2!1) => (X(l1!1) AND X(l2!1) => X(l1!1))) =>
       (X(l2!1) AND X(l1!1) AND X(l2!1)) => X(l1!1)
[-2]  (X(l2!1 => l1!1 AND l2!1) => X(l1!1) IMPLIES X(l1!1 AND l2!1)) AND
       ((X(l1!1) IMPLIES X(l1!1 AND l2!1)) => X(l1!1))
       => (X(l2!1 => l1!1 AND l2!1) => X(l1!1))
[-3]  X(l2!1 IMPLIES l1!1 AND l2!1) => X(l2!1) IMPLIES X(l1!1 AND l2!1)
[-4]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (X(l1!1) => X(l2!1 => l1!1 AND l2!1))
[-5]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.2.1.

T3.2.2 :  

  |-------
{1}   l1!1 => (l2!1 => l1!1 AND l2!1)
[2]   X(l1!1) AND X(l2!1) IMPLIES X(l1!1 AND l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.2.2.


This completes the proof of T3.2.

T3.1 :  

  |-------
{1}   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (case "l1!1 and l2!1 => l1!1")
Case splitting on 
   l1!1 AND l2!1 => l1!1, 
this yields  2 subgoals: 
T3.1.1 :  

{-1}  l1!1 AND l2!1 => l1!1
  |-------
[1]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (lemma RXmon)
Applying RXmon 
this simplifies to: 
T3.1.1 :  

{-1}  FORALL (l1, l2: LTL): (l1 => l2) => (X(l1) => X(l2))
[-2]  l1!1 AND l2!1 => l1!1
  |-------
[1]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (inst -1 "l1!1 and l2!1" "l1!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 and l2!1, l1!1,
this simplifies to: 
T3.1.1 :  

{-1}  (l1!1 AND l2!1 => l1!1) => (X(l1!1 AND l2!1) => X(l1!1))
[-2]  l1!1 AND l2!1 => l1!1
  |-------
[1]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (case "l1!1 and l2!1 => l2!1")
Case splitting on 
   l1!1 AND l2!1 => l2!1, 
this yields  2 subgoals: 
T3.1.1.1 :  

{-1}  l1!1 AND l2!1 => l2!1
[-2]  (l1!1 AND l2!1 => l1!1) => (X(l1!1 AND l2!1) => X(l1!1))
[-3]  l1!1 AND l2!1 => l1!1
  |-------
[1]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (lemma RXmon)
Applying RXmon 
this simplifies to: 
T3.1.1.1 :  

{-1}  FORALL (l1, l2: LTL): (l1 => l2) => (X(l1) => X(l2))
[-2]  l1!1 AND l2!1 => l2!1
[-3]  (l1!1 AND l2!1 => l1!1) => (X(l1!1 AND l2!1) => X(l1!1))
[-4]  l1!1 AND l2!1 => l1!1
  |-------
[1]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (inst -1 "l1!1 and l2!1" "l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 and l2!1, l2!1,
this simplifies to: 
T3.1.1.1 :  

{-1}  (l1!1 AND l2!1 => l2!1) => (X(l1!1 AND l2!1) => X(l2!1))
[-2]  l1!1 AND l2!1 => l2!1
[-3]  (l1!1 AND l2!1 => l1!1) => (X(l1!1 AND l2!1) => X(l1!1))
[-4]  l1!1 AND l2!1 => l1!1
  |-------
[1]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (lemma R5)
Applying R5 
this simplifies to: 
T3.1.1.1 :  

{-1}  FORALL (l, l1, l2: LTL): (l => l1) AND (l => l2) => (l => l1 AND l2)
[-2]  (l1!1 AND l2!1 => l2!1) => (X(l1!1 AND l2!1) => X(l2!1))
[-3]  l1!1 AND l2!1 => l2!1
[-4]  (l1!1 AND l2!1 => l1!1) => (X(l1!1 AND l2!1) => X(l1!1))
[-5]  l1!1 AND l2!1 => l1!1
  |-------
[1]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (inst -1 "X(l1!1 and l2!1)" "X(l1!1)" "X(l2!1)")
Instantiating the top quantifier in -1 with the terms: 
 X(l1!1 and l2!1), X(l1!1), X(l2!1),
this simplifies to: 
T3.1.1.1 :  

{-1}  (X(l1!1 AND l2!1) => X(l1!1)) AND (X(l1!1 AND l2!1) => X(l2!1)) =>
       (X(l1!1 AND l2!1) => X(l1!1) AND X(l2!1))
[-2]  (l1!1 AND l2!1 => l2!1) => (X(l1!1 AND l2!1) => X(l2!1))
[-3]  l1!1 AND l2!1 => l2!1
[-4]  (l1!1 AND l2!1 => l1!1) => (X(l1!1 AND l2!1) => X(l1!1))
[-5]  l1!1 AND l2!1 => l1!1
  |-------
[1]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.1.1.1.

T3.1.1.2 :  

[-1]  (l1!1 AND l2!1 => l1!1) => (X(l1!1 AND l2!1) => X(l1!1))
[-2]  l1!1 AND l2!1 => l1!1
  |-------
{1}   l1!1 AND l2!1 => l2!1
[2]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.1.1.2.


This completes the proof of T3.1.1.

T3.1.2 :  

  |-------
{1}   l1!1 AND l2!1 => l1!1
[2]   X(l1!1 AND l2!1) IMPLIES X(l1!1) AND X(l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.1.2.


This completes the proof of T3.1.

Q.E.D.
```



##### ==T4==

![image-20201218171729813](LTL证明5.assets\image-20201218171729813.png

```
T4 :  

  |-------
{1}   FORALL (l: LTL): G(l) => X(G(l))

Rule? (skolem!)
Skolemizing,
this simplifies to: 
T4 :  

  |-------
{1}   G(l!1) => X(G(l!1))

Rule? (lemma F6)
Applying F6 
this simplifies to: 
T4 :  

{-1}  FORALL (l1, l2: LTL): U(l1, l2) <=> (l2 OR l1 AND X(U(l1, l2)))
  |-------
[1]   G(l!1) => X(G(l!1))

Rule? (inst -1 "true" "not l!1")
Instantiating the top quantifier in -1 with the terms: 
 true, not l!1,
this simplifies to: 
T4 :  

{-1}  U(TRUE, NOT l!1) <=> (NOT l!1 OR X(U(TRUE, NOT l!1)))
  |-------
[1]   G(l!1) => X(G(l!1))

Rule? (lemma F1)
Applying F1 
this simplifies to: 
T4 :  

{-1}  FORALL (l: LTL): X(NOT l) <=> NOT (X(l))
[-2]  U(TRUE, NOT l!1) <=> (NOT l!1 OR X(U(TRUE, NOT l!1)))
  |-------
[1]   G(l!1) => X(G(l!1))

Rule? (inst -1 "U(true , not l!1)")
Instantiating the top quantifier in -1 with the terms: 
 U(true , not l!1),
this simplifies to: 
T4 :  

{-1}  X(NOT U(TRUE, NOT l!1)) <=> NOT (X(U(TRUE, NOT l!1)))
[-2]  U(TRUE, NOT l!1) <=> (NOT l!1 OR X(U(TRUE, NOT l!1)))
  |-------
[1]   G(l!1) => X(G(l!1))

Rule? (expand G)
Expanding the definition of G,
this simplifies to: 
T4 :  

[-1]  X(NOT U(TRUE, NOT l!1)) <=> NOT (X(U(TRUE, NOT l!1)))
[-2]  U(TRUE, NOT l!1) <=> (NOT l!1 OR X(U(TRUE, NOT l!1)))
  |-------
{1}   R(FALSE, l!1) => X(R(FALSE, l!1))

Rule? (lemma E_TRNS)
Applying E_TRNS 
this simplifies to: 
T4 :  

{-1}  FORALL (l, l1, l2: LTL): (l1 => l2) AND (l2 => l) => (l1 => l)
[-2]  X(NOT U(TRUE, NOT l!1)) <=> NOT (X(U(TRUE, NOT l!1)))
[-3]  U(TRUE, NOT l!1) <=> (NOT l!1 OR X(U(TRUE, NOT l!1)))
  |-------
[1]   R(FALSE, l!1) => X(R(FALSE, l!1))

Rule? (grind)
R rewrites R(FALSE, FALSE)
  to NOT (U(TRUE, TRUE))
R rewrites R(FALSE, TRUE)
  to NOT (U(TRUE, FALSE))
R rewrites R(FALSE, l!1)
  to FALSE
Trying repeated skolemization, instantiation, and if-lifting,
Q.E.D.
```



##### RGgen

![image-20201218171736200](LTL证明5.assets\image-20201218171736200.png

```
RGgen :  

  |-------
{1}   FORALL (l: LTL): G(l) => G(G(l))

Rule? (skolem!)
Skolemizing,
this simplifies to: 
RGgen :  

  |-------
{1}   G(l!1) => G(G(l!1))

Rule? (lemma F5)
Applying F5 
this simplifies to: 
RGgen :  

{-1}  FORALL (l: LTL): (l IMPLIES X(l)) => (l IMPLIES G(l))
  |-------
[1]   G(l!1) => G(G(l!1))

Rule? (inst -1 "G(l!1)")
Instantiating the top quantifier in -1 with the terms: 
 G(l!1),
this simplifies to: 
RGgen :  

{-1}  (G(l!1) IMPLIES X(G(l!1))) => (G(l!1) IMPLIES G(G(l!1)))
  |-------
[1]   G(l!1) => G(G(l!1))

Rule? (lemma T4)
Applying T4 
this simplifies to: 
RGgen :  

{-1}  FORALL (l: LTL): G(l) => X(G(l))
[-2]  (G(l!1) IMPLIES X(G(l!1))) => (G(l!1) IMPLIES G(G(l!1)))
  |-------
[1]   G(l!1) => G(G(l!1))

Rule? (inst -1 l!1)
Instantiating the top quantifier in -1 with the terms: 
 l!1,
this simplifies to: 
RGgen :  

{-1}  G(l!1) => X(G(l!1))
[-2]  (G(l!1) IMPLIES X(G(l!1))) => (G(l!1) IMPLIES G(G(l!1)))
  |-------
[1]   G(l!1) => G(G(l!1))

Rule? (forward-chain MP)
Forward chaining on MP,
Q.E.D.
```



##### RGmon

![image-20201218171742269](LTL证明5.assets\image-20201218171742269.png)

```
RGmon :  

  |-------
{1}   FORALL (l1, l2: LTL): (l1 => l2) => (G(l1) => G(l2))

Rule? (skolem!)
Skolemizing,
this simplifies to: 
RGmon :  

  |-------
{1}   (l1!1 => l2!1) => (G(l1!1) => G(l2!1))

Rule? (lemma F3)
Applying F3 
this simplifies to: 
RGmon :  

{-1}  FORALL (l1, l2: LTL): G(l1 IMPLIES l2) => G(l1) IMPLIES G(l2)
  |-------
[1]   (l1!1 => l2!1) => (G(l1!1) => G(l2!1))

Rule? (inst?)
Found substitution:
l2: LTL gets l2!1,
l1: LTL gets l1!1,
Using template: G(l1) IMPLIES G(l2)
Instantiating quantified variables,
this simplifies to: 
RGmon :  

{-1}  G(l1!1 IMPLIES l2!1) => G(l1!1) IMPLIES G(l2!1)
  |-------
[1]   (l1!1 => l2!1) => (G(l1!1) => G(l2!1))

Rule? (lemma RGgen)
Applying RGgen 
this simplifies to: 
RGmon :  

{-1}  FORALL (l: LTL): G(l) => G(G(l))
[-2]  G(l1!1 IMPLIES l2!1) => G(l1!1) IMPLIES G(l2!1)
  |-------
[1]   (l1!1 => l2!1) => (G(l1!1) => G(l2!1))

Rule? (inst -1 "l1!1 implies l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 implies l2!1,
this simplifies to: 
RGmon :  

{-1}  G(l1!1 IMPLIES l2!1) => G(G(l1!1 IMPLIES l2!1))
[-2]  G(l1!1 IMPLIES l2!1) => G(l1!1) IMPLIES G(l2!1)
  |-------
[1]   (l1!1 => l2!1) => (G(l1!1) => G(l2!1))

Rule? (flatten 1)
Applying disjunctive simplification to flatten sequent,
this simplifies to: 
RGmon :  

[-1]  G(l1!1 IMPLIES l2!1) => G(G(l1!1 IMPLIES l2!1))
[-2]  G(l1!1 IMPLIES l2!1) => G(l1!1) IMPLIES G(l2!1)
{-3}  (l1!1 => l2!1)
{-4}  G(l1!1)
  |-------
{1}   G(l2!1)

Rule? (lemma INS)
Applying INS 
this simplifies to: 
RGmon :  

{-1}  FORALL (l: LTL): l => G(l)
[-2]  G(l1!1 IMPLIES l2!1) => G(G(l1!1 IMPLIES l2!1))
[-3]  G(l1!1 IMPLIES l2!1) => G(l1!1) IMPLIES G(l2!1)
[-4]  (l1!1 => l2!1)
[-5]  G(l1!1)
  |-------
[1]   G(l2!1)

Rule? (inst -1 " l1!1 IMPLIES l2!1")
Instantiating the top quantifier in -1 with the terms: 
  l1!1 IMPLIES l2!1,
this simplifies to: 
RGmon :  

{-1}  (l1!1 IMPLIES l2!1) => G(l1!1 IMPLIES l2!1)
[-2]  G(l1!1 IMPLIES l2!1) => G(G(l1!1 IMPLIES l2!1))
[-3]  G(l1!1 IMPLIES l2!1) => G(l1!1) IMPLIES G(l2!1)
[-4]  (l1!1 => l2!1)
[-5]  G(l1!1)
  |-------
[1]   G(l2!1)

Rule? (lemma E_MP)
Applying E_MP 
this simplifies to: 
RGmon :  

{-1}  FORALL (l, l1, l2: LTL):
        ((l1 AND l2) => l) AND G(l1) AND G(l2) => G(l)
[-2]  (l1!1 IMPLIES l2!1) => G(l1!1 IMPLIES l2!1)
[-3]  G(l1!1 IMPLIES l2!1) => G(G(l1!1 IMPLIES l2!1))
[-4]  G(l1!1 IMPLIES l2!1) => G(l1!1) IMPLIES G(l2!1)
[-5]  (l1!1 => l2!1)
[-6]  G(l1!1)
  |-------
[1]   G(l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,
Q.E.D.
```

##### T5

![image-20201218171749489](LTL证明5.assets\image-20201218171749489.png)



```
T5 :  

  |-------
{1}   FORALL (l1, l2: LTL): G(l1) OR G(l2) => G(l1 OR l2)

Rule? (skolem!)
Skolemizing,
this simplifies to: 
T5 :  

  |-------
{1}   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (case "l1!1 => l1!1 or l2!1")
Case splitting on 
   l1!1 => l1!1 OR l2!1, 
this yields  2 subgoals: 
T5.1 :  

{-1}  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (lemma RGmon)
Applying RGmon 
this simplifies to: 
T5.1 :  

{-1}  FORALL (l1, l2: LTL): (l1 => l2) => (G(l1) => G(l2))
[-2]  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (inst?)
Found substitution:
l2: LTL gets l1!1 OR l2!1,
l1: LTL gets l1!1,
Using template: (l1 => l2)
Instantiating quantified variables,
this simplifies to: 
T5.1 :  

{-1}  (l1!1 => l1!1 OR l2!1) => (G(l1!1) => G(l1!1 OR l2!1))
[-2]  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (case "l2!1 => l1!1 or l2!1 ")
Case splitting on 
   l2!1 => l1!1 OR l2!1, 
this yields  2 subgoals: 
T5.1.1 :  

{-1}  l2!1 => l1!1 OR l2!1
[-2]  (l1!1 => l1!1 OR l2!1) => (G(l1!1) => G(l1!1 OR l2!1))
[-3]  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (lemma RGmon)
Applying RGmon 
this simplifies to: 
T5.1.1 :  

{-1}  FORALL (l1, l2: LTL): (l1 => l2) => (G(l1) => G(l2))
[-2]  l2!1 => l1!1 OR l2!1
[-3]  (l1!1 => l1!1 OR l2!1) => (G(l1!1) => G(l1!1 OR l2!1))
[-4]  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (inst -1 "l2!1" "l1!1 or l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l2!1, l1!1 or l2!1,
this simplifies to: 
T5.1.1 :  

{-1}  (l2!1 => l1!1 OR l2!1) => (G(l2!1) => G(l1!1 OR l2!1))
[-2]  l2!1 => l1!1 OR l2!1
[-3]  (l1!1 => l1!1 OR l2!1) => (G(l1!1) => G(l1!1 OR l2!1))
[-4]  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (lemma R4)
Applying R4 
this simplifies to: 
T5.1.1 :  

{-1}  FORALL (l, l1, l2: LTL): (l1 => l) AND (l2 => l) => (l1 AND l2 => l)
[-2]  (l2!1 => l1!1 OR l2!1) => (G(l2!1) => G(l1!1 OR l2!1))
[-3]  l2!1 => l1!1 OR l2!1
[-4]  (l1!1 => l1!1 OR l2!1) => (G(l1!1) => G(l1!1 OR l2!1))
[-5]  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (inst?)
Found substitution:
l: LTL gets l1!1 OR l2!1,
l1: LTL gets l2!1,
Using template: (l1 => l)
Instantiating quantified variables,
this simplifies to: 
T5.1.1 :  

{-1}  FORALL (l2: LTL):
        (l2!1 => l1!1 OR l2!1) AND (l2 => l1!1 OR l2!1) =>
         (l2!1 AND l2 => l1!1 OR l2!1)
[-2]  (l2!1 => l1!1 OR l2!1) => (G(l2!1) => G(l1!1 OR l2!1))
[-3]  l2!1 => l1!1 OR l2!1
[-4]  (l1!1 => l1!1 OR l2!1) => (G(l1!1) => G(l1!1 OR l2!1))
[-5]  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (inst?)
Found substitution:
l2: LTL gets l2!1,
Using template: l2
Instantiating quantified variables,
this simplifies to: 
T5.1.1 :  

{-1}  (l2!1 => l1!1 OR l2!1) AND (l2!1 => l1!1 OR l2!1) =>
       (l2!1 AND l2!1 => l1!1 OR l2!1)
[-2]  (l2!1 => l1!1 OR l2!1) => (G(l2!1) => G(l1!1 OR l2!1))
[-3]  l2!1 => l1!1 OR l2!1
[-4]  (l1!1 => l1!1 OR l2!1) => (G(l1!1) => G(l1!1 OR l2!1))
[-5]  l1!1 => l1!1 OR l2!1
  |-------
[1]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T5.1.1.

T5.1.2 :  

[-1]  (l1!1 => l1!1 OR l2!1) => (G(l1!1) => G(l1!1 OR l2!1))
[-2]  l1!1 => l1!1 OR l2!1
  |-------
{1}   l2!1 => l1!1 OR l2!1
[2]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T5.1.2.


This completes the proof of T5.1.

T5.2 :  

  |-------
{1}   l1!1 => l1!1 OR l2!1
[2]   G(l1!1) OR G(l2!1) => G(l1!1 OR l2!1)

Rule?(grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T5.2.

Q.E.D.
```

#####==T6==

![image-20201218171800336](LTL证明5.assets\image-20201218171800336.png)

```
T6 :  

  |-------
{1}   FORALL (l1, l2: LTL): G(l1 AND l2) <=> G(l1) AND G(l2)

Rule? (skolem!)
Skolemizing,
this simplifies to: 
T6 :  

  |-------
{1}   G(l1!1 AND l2!1) <=> G(l1!1) AND G(l2!1)

Rule? (split)
Splitting conjunctions,
this yields  2 subgoals: 
T6.1 :  

  |-------
{1}   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (postpone)
Postponing T6.1.

T6.2 :  

  |-------
{1}   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (case "l1!1 => (l2!1 => l1!1 and l2!1)")
Case splitting on 
   l1!1 => (l2!1 => l1!1 AND l2!1), 
this yields  2 subgoals: 
T6.2.1 :  

{-1}  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (lemma RGmon)
Applying RGmon 
this simplifies to: 
T6.2.1 :  

{-1}  FORALL (l1, l2: LTL): (l1 => l2) => (G(l1) => G(l2))
[-2]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (inst -1 "l1!1" "l2!1 => l1!1 AND l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1, l2!1 => l1!1 AND l2!1,
this simplifies to: 
T6.2.1 :  

{-1}  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (G(l1!1) => G(l2!1 => l1!1 AND l2!1))
[-2]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (lemma F3)
Applying F3 
this simplifies to: 
T6.2.1 :  

{-1}  FORALL (l1, l2: LTL): G(l1 IMPLIES l2) => G(l1) IMPLIES G(l2)
[-2]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (G(l1!1) => G(l2!1 => l1!1 AND l2!1))
[-3]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (inst -1 "l2!1" "l1!1 AND l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l2!1, l1!1 AND l2!1,
this simplifies to: 
T6.2.1 :  

{-1}  G(l2!1 IMPLIES l1!1 AND l2!1) => G(l2!1) IMPLIES G(l1!1 AND l2!1)
[-2]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (G(l1!1) => G(l2!1 => l1!1 AND l2!1))
[-3]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (lemma E_TRNS)
Applying E_TRNS 
this simplifies to: 
T6.2.1 :  

{-1}  FORALL (l, l1, l2: LTL): (l1 => l2) AND (l2 => l) => (l1 => l)
[-2]  G(l2!1 IMPLIES l1!1 AND l2!1) => G(l2!1) IMPLIES G(l1!1 AND l2!1)
[-3]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (G(l1!1) => G(l2!1 => l1!1 AND l2!1))
[-4]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule?  (inst -1 " G(l1!1)" "G(l2!1 => l1!1 AND l2!1)"
                 "G(l2!1) IMPLIES G(l1!1 AND l2!1)")
Instantiating the top quantifier in -1 with the terms: 
  G(l1!1), G(l2!1 => l1!1 AND l2!1), G(l2!1) IMPLIES G(l1!1 AND l2!1),
this simplifies to: 
T6.2.1 :  

{-1}  (G(l2!1 => l1!1 AND l2!1) => G(l2!1) IMPLIES G(l1!1 AND l2!1)) AND
       ((G(l2!1) IMPLIES G(l1!1 AND l2!1)) => G(l1!1))
       => (G(l2!1 => l1!1 AND l2!1) => G(l1!1))
[-2]  G(l2!1 IMPLIES l1!1 AND l2!1) => G(l2!1) IMPLIES G(l1!1 AND l2!1)
[-3]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (G(l1!1) => G(l2!1 => l1!1 AND l2!1))
[-4]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (lemma R7)
Applying R7 
this simplifies to: 
T6.2.1 :  

{-1}  FORALL (l, l1, l2: LTL): (l1 => (l2 => l)) => (l1 AND l2) => l
[-2]  (G(l2!1 => l1!1 AND l2!1) => G(l2!1) IMPLIES G(l1!1 AND l2!1)) AND
       ((G(l2!1) IMPLIES G(l1!1 AND l2!1)) => G(l1!1))
       => (G(l2!1 => l1!1 AND l2!1) => G(l1!1))
[-3]  G(l2!1 IMPLIES l1!1 AND l2!1) => G(l2!1) IMPLIES G(l1!1 AND l2!1)
[-4]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (G(l1!1) => G(l2!1 => l1!1 AND l2!1))
[-5]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule?  (inst -1 "G(l1!1)" "G(l2!1)" "G(l1!1 and l2!1)")
Instantiating the top quantifier in -1 with the terms: 
 G(l1!1), G(l2!1), G(l1!1 and l2!1),
this simplifies to: 
T6.2.1 :  

{-1}  (G(l2!1) => (G(l1!1 AND l2!1) => G(l1!1))) =>
       (G(l2!1) AND G(l1!1 AND l2!1)) => G(l1!1)
[-2]  (G(l2!1 => l1!1 AND l2!1) => G(l2!1) IMPLIES G(l1!1 AND l2!1)) AND
       ((G(l2!1) IMPLIES G(l1!1 AND l2!1)) => G(l1!1))
       => (G(l2!1 => l1!1 AND l2!1) => G(l1!1))
[-3]  G(l2!1 IMPLIES l1!1 AND l2!1) => G(l2!1) IMPLIES G(l1!1 AND l2!1)
[-4]  (l1!1 => l2!1 => l1!1 AND l2!1) =>
       (G(l1!1) => G(l2!1 => l1!1 AND l2!1))
[-5]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T6.2.1.

T6.2.2 :  

  |-------
{1}   l1!1 => (l2!1 => l1!1 AND l2!1)
[2]   G(l1!1) AND G(l2!1) IMPLIES G(l1!1 AND l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T6.2.2.


This completes the proof of T6.2.

T6.1 :  

  |-------
{1}   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (case "l1!1 and l2!1 => l1!1")
Case splitting on 
   l1!1 AND l2!1 => l1!1, 
this yields  2 subgoals: 
T6.1.1 :  

{-1}  l1!1 AND l2!1 => l1!1
  |-------
[1]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (case "l1!1 and l2!1 => l2!1")
Case splitting on 
   l1!1 AND l2!1 => l2!1, 
this yields  2 subgoals: 
T6.1.1.1 :  

{-1}  l1!1 AND l2!1 => l2!1
[-2]  l1!1 AND l2!1 => l1!1
  |-------
[1]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (lemma RGmon)
Applying RGmon 
this simplifies to: 
T6.1.1.1 :  

{-1}  FORALL (l1, l2: LTL): (l1 => l2) => (G(l1) => G(l2))
[-2]  l1!1 AND l2!1 => l2!1
[-3]  l1!1 AND l2!1 => l1!1
  |-------
[1]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (inst -1 "l1!1 AND l2!1" "l1!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 AND l2!1, l1!1,
this simplifies to: 
T6.1.1.1 :  

{-1}  (l1!1 AND l2!1 => l1!1) => (G(l1!1 AND l2!1) => G(l1!1))
[-2]  l1!1 AND l2!1 => l2!1
[-3]  l1!1 AND l2!1 => l1!1
  |-------
[1]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (lemma RGmon)
Applying RGmon 
this simplifies to: 
T6.1.1.1 :  

{-1}  FORALL (l1, l2: LTL): (l1 => l2) => (G(l1) => G(l2))
[-2]  (l1!1 AND l2!1 => l1!1) => (G(l1!1 AND l2!1) => G(l1!1))
[-3]  l1!1 AND l2!1 => l2!1
[-4]  l1!1 AND l2!1 => l1!1
  |-------
[1]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (inst -1 "l1!1 AND l2!1" "l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 AND l2!1, l2!1,
this simplifies to: 
T6.1.1.1 :  

{-1}  (l1!1 AND l2!1 => l2!1) => (G(l1!1 AND l2!1) => G(l2!1))
[-2]  (l1!1 AND l2!1 => l1!1) => (G(l1!1 AND l2!1) => G(l1!1))
[-3]  l1!1 AND l2!1 => l2!1
[-4]  l1!1 AND l2!1 => l1!1
  |-------
[1]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (lemma R5)
Applying R5 
this simplifies to: 
T6.1.1.1 :  

{-1}  FORALL (l, l1, l2: LTL): (l => l1) AND (l => l2) => (l => l1 AND l2)
[-2]  (l1!1 AND l2!1 => l2!1) => (G(l1!1 AND l2!1) => G(l2!1))
[-3]  (l1!1 AND l2!1 => l1!1) => (G(l1!1 AND l2!1) => G(l1!1))
[-4]  l1!1 AND l2!1 => l2!1
[-5]  l1!1 AND l2!1 => l1!1
  |-------
[1]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (inst?)
Found substitution:
l2: LTL gets G(l2!1),
l1: LTL gets G(l1!1),
l: LTL gets G(l1!1 AND l2!1),
Using template: (l => l1 AND l2)
Instantiating quantified variables,
this simplifies to: 
T6.1.1.1 :  

{-1}  (G(l1!1 AND l2!1) => G(l1!1)) AND (G(l1!1 AND l2!1) => G(l2!1)) =>
       (G(l1!1 AND l2!1) => G(l1!1) AND G(l2!1))
[-2]  (l1!1 AND l2!1 => l2!1) => (G(l1!1 AND l2!1) => G(l2!1))
[-3]  (l1!1 AND l2!1 => l1!1) => (G(l1!1 AND l2!1) => G(l1!1))
[-4]  l1!1 AND l2!1 => l2!1
[-5]  l1!1 AND l2!1 => l1!1
  |-------
[1]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T6.1.1.1.

T6.1.1.2 :  

[-1]  l1!1 AND l2!1 => l1!1
  |-------
{1}   l1!1 AND l2!1 => l2!1
[2]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T6.1.1.2.


This completes the proof of T6.1.1.

T6.1.2 :  

  |-------
{1}   l1!1 AND l2!1 => l1!1
[2]   G(l1!1 AND l2!1) IMPLIES G(l1!1) AND G(l2!1)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T6.1.2.


This completes the proof of T6.1.

Q.E.D.
```

#####U_double

```
U_double :  

  |-------
{1}   FORALL (l1, l2: LTL): U(l1, l2) <=> U(l1, U(l1, l2))

Rule? (skosimp)
Skolemizing and flattening,
this simplifies to: 
U_double :  

  |-------
{1}   U(l1!1, l2!1) <=> U(l1!1, U(l1!1, l2!1))

Rule? (lemma U2R)
Applying U2R 
this simplifies to: 
U_double :  

{-1}  FORALL (l1, l2: LTL): NOT U(l1, l2) <=> R(NOT l1, NOT l2)
  |-------
[1]   U(l1!1, l2!1) <=> U(l1!1, U(l1!1, l2!1))

Rule? (lemma R2W)
Applying R2W 
this simplifies to: 
U_double :  

{-1}  FORALL (l1, l2: LTL): R(l1, l2) <=> W(l2, l1 AND l2)
[-2]  FORALL (l1, l2: LTL): NOT U(l1, l2) <=> R(NOT l1, NOT l2)
  |-------
[1]   U(l1!1, l2!1) <=> U(l1!1, U(l1!1, l2!1))

Rule? (lemma W2U_G)
Applying W2U_G 
this simplifies to: 
U_double :  

{-1}  FORALL (l1, l2: LTL): W(l1, l2) <=> U(l1, l2) OR G(l1)
[-2]  FORALL (l1, l2: LTL): R(l1, l2) <=> W(l2, l1 AND l2)
[-3]  FORALL (l1, l2: LTL): NOT U(l1, l2) <=> R(NOT l1, NOT l2)
  |-------
[1]   U(l1!1, l2!1) <=> U(l1!1, U(l1!1, l2!1))

Rule? (grind)
R rewrites R(FALSE, l1)
  to NOT (U(TRUE, NOT l1))
....
....
R rewrites R(TRUE, TRUE)
  to TRUE
Trying repeated skolemization, instantiation, and if-lifting,
this yields  3 subgoals: 
U_double.1 :  

{-1}  W(l1!1, FALSE)
{-2}  (U(FALSE, FALSE))
{-3}  (U(TRUE, FALSE))
{-4}  U(l1!1, FALSE)
  |-------
{1}   U(TRUE, TRUE)
{2}   R(TRUE, TRUE)
{3}   U(l1!1, TRUE)

Rule? (lemma F6)
Applying F6 
this simplifies to: 
U_double.1 :  

{-1}  FORALL (l1, l2: LTL): U(l1, l2) <=> (l2 OR l1 AND X(U(l1, l2)))
[-2]  W(l1!1, FALSE)
[-3]  (U(FALSE, FALSE))
[-4]  (U(TRUE, FALSE))
[-5]  U(l1!1, FALSE)
  |-------
[1]   U(TRUE, TRUE)
[2]   R(TRUE, TRUE)
[3]   U(l1!1, TRUE)

Rule? (inst -1 true true)
Instantiating the top quantifier in -1 with the terms: 
 true, true,
this simplifies to: 
U_double.1 :  

{-1}  U(TRUE, TRUE) <=> TRUE
[-2]  W(l1!1, FALSE)
[-3]  (U(FALSE, FALSE))
[-4]  (U(TRUE, FALSE))
[-5]  U(l1!1, FALSE)
  |-------
[1]   U(TRUE, TRUE)
[2]   R(TRUE, TRUE)
[3]   U(l1!1, TRUE)

Rule? (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of U_double.1.

U_double.2 :  

{-1}  (U(FALSE, FALSE))
{-2}  (U(TRUE, FALSE))
{-3}  U(l1!1, FALSE)
  |-------
{1}   U(TRUE, TRUE)
{2}   R(TRUE, TRUE)
{3}   U(l1!1, l2!1)

Rule? (同U_double.1)
This completes the proof of U_double.2.

U_double.3 :  

{-1}  U(TRUE, TRUE)
{-2}  U(l1!1, FALSE)
  |-------
{1}   (U(FALSE, FALSE))
{2}   R(FALSE, FALSE)
{3}   U(l1!1, l2!1)

Rule? (lemma F7)
Applying F7 
this simplifies to: 
U_double.3 :  

{-1}  FORALL (l1, l2: LTL): U(l1, l2) => F(l2)
[-2]  U(TRUE, TRUE)
[-3]  U(l1!1, FALSE)
  |-------
[1]   (U(FALSE, FALSE))
[2]   R(FALSE, FALSE)
[3]   U(l1!1, l2!1)

Rule? (inst -1 l1!1 false)
Instantiating the top quantifier in -1 with the terms: 
 l1!1, false,
this simplifies to: 
U_double.3 :  

{-1}  U(l1!1, FALSE) => F(FALSE)
[-2]  U(TRUE, TRUE)
[-3]  U(l1!1, FALSE)
  |-------
[1]   (U(FALSE, FALSE))
[2]   R(FALSE, FALSE)
[3]   U(l1!1, l2!1)

Rule? (lemma Fnot_notG)
Applying Fnot_notG 
this simplifies to: 
U_double.3 :  

{-1}  FORALL (l1: LTL): F(NOT l1) <=> NOT G(l1)
[-2]  U(l1!1, FALSE) => F(FALSE)
[-3]  U(TRUE, TRUE)
[-4]  U(l1!1, FALSE)
  |-------
[1]   (U(FALSE, FALSE))
[2]   R(FALSE, FALSE)
[3]   U(l1!1, l2!1)

Rule? (inst -1 true)
Instantiating the top quantifier in -1 with the terms: 
 true,
this simplifies to: 
U_double.3 :  

{-1}  F(FALSE) <=> NOT G(TRUE)
[-2]  U(l1!1, FALSE) => F(FALSE)
[-3]  U(TRUE, TRUE)
[-4]  U(l1!1, FALSE)
  |-------
[1]   (U(FALSE, FALSE))
[2]   R(FALSE, FALSE)
[3]   U(l1!1, l2!1)

Rule? (grind)
F rewrites F(FALSE)
  to U(TRUE, FALSE)
R rewrites R(FALSE, TRUE)
  to FALSE
G rewrites G(TRUE)
  to FALSE
Trying repeated skolemization, instantiation, and if-lifting,
this simplifies to: 
U_double.3 :  

{-1}  U(TRUE, FALSE)
[-2]  U(l1!1, FALSE)
[-3]  U(TRUE, TRUE)
  |-------
{1}   G(TRUE)
[2]   (U(FALSE, FALSE))
[3]   R(FALSE, FALSE)
[4]   U(l1!1, l2!1)

Rule? (lemma INS)
Applying INS 
this simplifies to: 
U_double.3 :  

{-1}  FORALL (l: LTL): l => G(l)
[-2]  U(TRUE, FALSE)
[-3]  U(l1!1, FALSE)
[-4]  U(TRUE, TRUE)
  |-------
[1]   G(TRUE)
[2]   (U(FALSE, FALSE))
[3]   R(FALSE, FALSE)
[4]   U(l1!1, l2!1)

Rule? (inst -1 true)
Instantiating the top quantifier in -1 with the terms: 
 true,

This completes the proof of U_double.3.

Q.E.D.
```





```
PTL_temp  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

    LTL : nonempty_type = boolean

    p,q,r         : var bool
    l,l1,l2       : var LTL

    X  		  : [LTL -> LTL]
    U  		  : [LTL , LTL -> LTL]

    F(l)	  : LTL = U(true,l)
    R(l1,l2)	  : LTL = not (U(not l1 , not l2))
    G(l)	  : LTL = R(false , l)
    W(l1,l2)	  : LTL = U(l1,l2) or G(l1)


  

    %%% Future Axiom
    F0        :   axiom G(l) => l
    F1	      :   axiom X(not l) <=> not(X(l))
    F3	      :   axiom G(l1 implies l2) => G(l1) implies G(l2)
    F2	      :   axiom X(l1 implies l2) => X(l1) implies X(l2)
    F4	      :   axiom G(l) => G(X(l))
    F5	      :   axiom (l implies X(l)) => (l implies G(l))
    F6	      :   axiom U(l1,l2) <=> (l2 or l1 and X(U(l1,l2)))
    F7        :   axiom U(l1,l2) => F(l2)

    
    %%% Inference Rules
    INS       :  lemma  l => G(l)
    MP	      :  lemma  (l1 => l2) and l1 => l2


    %%% Theorems and Rules of The Deductive System

    %%% particularization
    PAR      :  lemma G(l) => l
    %%% entailment modus ponens
    E_MP     :  lemma ((l1 and l2) => l) and G(l1) and G(l2) => G(l)
    E_MP_01  :	lemma (l1 => l) and G(l1) => G(l)


    %%% entailment transitivity
    E_TRNS   :  lemma (l1 => l2) and (l2 => l) => (l1 => l)
    R1	     : 	lemma G(l2) => (l1 => l2)
    R2	     :	lemma (l1 => l2) => (not l2 => not l1)
    R3	     : 	lemma (l1 <=> l2) => ((l1 => l2) and (l2 => l1))
    R4	     :	lemma (l1 => l) and (l2 => l) => (l1 and l2 => l)
    R5	     :	lemma (l => l1) and (l => l2) => (l => l1 and l2)
    R6	     :	lemma (l1 => l2) and (l2 => l1) => (l1 <=> l2)
    R7	     :	lemma (l1 => (l2 => l)) => (l1 and l2) => l

    %%% computational induction
    Rci	     :	lemma (l => X(l)) => (l => G(l))
    %%% X generalization
    RXgen    :	lemma G(l) => G(X(l))
    %%% X is monotonic
    RXmon    :	lemma (l1 => l2) => (X(l1) => X(l2))

    T3	     :  theorem	  X(l1 and l2) <=> X(l1) and X(l2)
    T4	     :	theorem	  G(l) => X(G(l))   

    %%% G generalization
    RGgen    :	lemma G(l) => G(G(l))
    %%% G is monotonic
    RGmon    :	lemma (l1 => l2) => (G(l1) => G(l2))

    T5	     :	theorem	  G(l1) or G(l2) => G(l1 or l2)
    T6	     :	theorem	  G(l1 and l2) <=>  G(l1) and G(l2)



    U2F	     :  lemma U(true,l) <=> F(l)
    U2G	     :  lemma U(true , not l) <=> not G(l)
    U2R	     :  lemma not U(l1,l2) <=> R(not l1,not l2)

    
    W2R	     :  axiom W(l1,l2) <=> R(l2,l1 or l2)
    R2W      :  axiom R(l1,l2) <=> W(l2,l1 and l2)

    W2U_G    :  lemma W(l1,l2) <=> U(l1,l2) or G(l1)
    U2W_F    :  axiom U(l1,l2) <=> W(l1,l2) and F(l2)

    Fnot_notG    : lemma F(not l1) <=> not G(l1)
    G_not_notF   : lemma G(not l1) <=> not F(l1)


    U_double :	lemma U(l1,l2) <=> U(l1 , U(l1,l2))
    test     :  lemma U(l1,l2) <=> not(U(not l2,not l1 and not l2)) and F(l2)
    
    test1    :	lemma p and not p => q
    
  END PTL_temp

```

