







###  Propositional Temporal Logics



#### The Future Axiom Schemes

![image-20201218172034348](LTL证明6.assets\image-20201218172034348.png)

#### Inference Rules

![image-20201218172041472](LTL证明6.assets\image-20201218172041472.png)



####  pvs语法描述

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
 	SQT(l1,l2) 	  : bool = G(l1) => G(l2)   %%<=>    SQT(l1) => SQT<l2>
    SQT(l)     	  : bool = G(l)

    SQT_and    	  : axiom SQT(l1 and l2) <=> SQT(l1) and SQT(l2)
    SQT_not	  	  : axiom SQT(not l)	 <=> not SQT(l)
    G_and	 	  : axiom G(l1 and l2) 	 <=> G(l1) and G(l2)
```

#### Future Axiom

```
%%% Future Axiom
    F0        :   axiom SQT	(G(l) =>l)	
    F1	      :   axiom SQT(G(X(not l))) <=> SQT(G(not(X(l))))
    %%
    F3	      :   axiom SQT(G(G(l1 => l2) => (G(l1) => G(l2))))
    F2	      :   axiom SQT(G(X(l1 => l2) => X(l1) => X(l2)))
    F4	      :   axiom SQT(G(l) => G(X(l)))
    F5	      :   axiom SQT(G(l => X(l)) => G(l => G(l)))
    F6	      :   axiom SQT(G(U(l1,l2))) <=> SQT(G(l2 or l1 and X(U(l1,l2))))
    F7        :   axiom SQT(G(U(l1,l2) => F(l2)))
```

#### Inference Rules

```
  %%%Inference Rules
  %%%  INS       :  lemma  l => G(l)
  %%%  MP	     :  lemma  G(l1 => l2) and Gl1 => Gl2
    	     		SQT()
    				SQT(l1 => l2 and l1  ,  l2) 
  %%%SQT(a,b)    :	 G(a) => G(b)
    
   
    INS      :  lemma  (true => l) =>SQT(G(l))
    MP	     :  lemma  SQT(l1 => l2) and SQT(l1) => SQT(l2)
    
    PAR      :  lemma SQT(G(l)) => SQT(l)
    E_MP_01  :	lemma SQT(G(l1 => l))        and  SQT(G(l1))    => SQT(G(l))
    E_MP_02  :  lemma SQT(G(lSQT1 and l2 => l)) and  SQT(G(l1))   and SQT(G(l2))    						=> SQT(G(l))
```

####PAR-proof

```
PAR :  

  |-------
{1}   FORALL (l: LTL): SQT(G(l)) => SQT(l)

Rule? (skolem!)
Skolemizing,
this simplifies to: 
PAR :  

  |-------
{1}   SQT(G(l!1)) => SQT(l!1)

Rule? (lemma F0)
Applying F0 
this simplifies to: 
PAR :  

{-1}  FORALL (l: LTL): SQT(G(l) => l)
  |-------
[1]   SQT(G(l!1)) => SQT(l!1)

Rule? (inst?)
Found substitution:
l: LTL gets l!1,
Using template: G(l)
Instantiating quantified variables,
this simplifies to: 
PAR :  

{-1}  SQT(G(l!1) => l!1)
  |-------
[1]   SQT(G(l!1)) => SQT(l!1)

Rule? (flatten)
Applying disjunctive simplification to flatten sequent,
this simplifies to: 
PAR :  

[-1]  SQT(G(l!1) => l!1)
{-2}  SQT(G(l!1))
  |-------
{1}   SQT(l!1)

Rule? (forward-chain MP)
Forward chaining on MP,
Q.E.D.
```



####E_MP_01-proof

```
E_MP_01 :  

  |-------
{1}   FORALL (l, l1: LTL): SQT(G(l1 => l)) AND SQT(G(l1)) => SQT(G(l))

Rule? (skolem!)
Skolemizing,
this simplifies to: 
E_MP_01 :  

  |-------
{1}   SQT(G(l1!1 => l!1)) AND SQT(G(l1!1)) => SQT(G(l!1))

Rule? (lemma F3)
Applying F3 
this simplifies to: 
E_MP_01 :  

{-1}  FORALL (l1, l2: LTL): SQT(G(G(l1 => l2) => (G(l1) => G(l2))))
  |-------
[1]   SQT(G(l1!1 => l!1)) AND SQT(G(l1!1)) => SQT(G(l!1))

Rule? (inst -1 l1!1 l!1)
Instantiating the top quantifier in -1 with the terms: 
 l1!1, l!1,
this simplifies to: 
E_MP_01 :  

{-1}  SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1))))
  |-------
[1]   SQT(G(l1!1 => l!1)) AND SQT(G(l1!1)) => SQT(G(l!1))

Rule? (lemma PAR)
Applying PAR 
this simplifies to: 
E_MP_01 :  

{-1}  FORALL (l: LTL): SQT(G(l)) => SQT(l)
[-2]  SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1))))
  |-------
[1]   SQT(G(l1!1 => l!1)) AND SQT(G(l1!1)) => SQT(G(l!1))

Rule? (inst -1 "G(l1!1 => l!1) => (G(l1!1) => G(l!1))")
Instantiating the top quantifier in -1 with the terms: 
 G(l1!1 => l!1) => (G(l1!1) => G(l!1)),
this simplifies to: 
E_MP_01 :  

{-1}  SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1)))) =>
       SQT(G(l1!1 => l!1) => (G(l1!1) => G(l!1)))
[-2]  SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1))))
  |-------
[1]   SQT(G(l1!1 => l!1)) AND SQT(G(l1!1)) => SQT(G(l!1))

Rule? (split)
Splitting conjunctions,
this yields  2 subgoals: 
E_MP_01.1 :  

{-1}  SQT(G(l1!1 => l!1) => (G(l1!1) => G(l!1)))
[-2]  SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1))))
  |-------
[1]   SQT(G(l1!1 => l!1)) AND SQT(G(l1!1)) => SQT(G(l!1))

Rule? (flatten)
Applying disjunctive simplification to flatten sequent,
this simplifies to: 
E_MP_01.1 :  

[-1]  SQT(G(l1!1 => l!1) => (G(l1!1) => G(l!1)))
[-2]  SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1))))
{-3}  SQT(G(l1!1 => l!1))
{-4}  SQT(G(l1!1))
  |-------
{1}   SQT(G(l!1))

Rule? (forward-chain MP)
Forward chaining on MP,
this simplifies to: 
E_MP_01.1 :  

{-1}  SQT((G(l1!1) => G(l!1)))
[-2]  SQT(G(l1!1 => l!1) => (G(l1!1) => G(l!1)))
[-3]  SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1))))
[-4]  SQT(G(l1!1 => l!1))
[-5]  SQT(G(l1!1))
  |-------
[1]   SQT(G(l!1))

Rule? (forward-chain MP)
Forward chaining on MP,


This completes the proof of E_MP_01.1.

E_MP_01.2 :  

[-1]  SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1))))
  |-------
{1}   SQT(G(G(l1!1 => l!1) => (G(l1!1) => G(l!1))))
[2]   SQT(G(l1!1 => l!1)) AND SQT(G(l1!1)) => SQT(G(l!1))

which is trivially true.

This completes the proof of E_MP_01.2.

Q.E.D.
```

####some lemmas

```
 %%% entailment transitivity
    E_TRNS   :  lemma SQT(G(l1 => l2))	and   SQT(G(l2 => l))  =>    SQT(G(l1 => l))
    R4	     :	lemma SQT(G(l1 => l))   and   SQT(G(l2 => l))  =>    SQT(G(l1 or l2 => l))
    R5	     :	lemma SQT(G(l => l1))   and   SQT(G(l => l2))  =>    SQT(G(l => l1 and l2))
    R6	     :	lemma SQT(G(l1 => l2))  and   SQT(G(l2 => l1)) =>    SQT(G(l1 <=> l2))
    R1	     : 	lemma SQT(G(l2))   	    =>   SQT(G(l1 => l2))
    R2	     :	lemma SQT(G(l1 => l2))  =>   SQT(G(l1 => l2))  and SQT(G(l2 => l1))
    R7	     :	lemma SQT(G(l1 => (l2 =>l))) =>   SQT(G(l1 and l2 => l))
```

####R1-proof



```
R1 :  

  |-------
{1}   FORALL (l1, l2: LTL): SQT(G(l2)) => SQT(G(l1 => l2))

Rerunning step: (skolem!)
Skolemizing,
this simplifies to: 
R1 :  

  |-------
{1}   SQT(G(l2!1)) => SQT(G(l1!1 => l2!1))

Rerunning step: (flatten)
Applying disjunctive simplification to flatten sequent,
this simplifies to: 
R1 :  

{-1}  SQT(G(l2!1))
  |-------
{1}   SQT(G(l1!1 => l2!1))

Rerunning step: (case "l2!1 => (l1!1 => l2!1)")
Case splitting on 
   l2!1 => (l1!1 => l2!1), 
this yields  2 subgoals: 
R1.1 :  

{-1}  l2!1 => (l1!1 => l2!1)
[-2]  SQT(G(l2!1))
  |-------
[1]   SQT(G(l1!1 => l2!1))

Rerunning step: (lemma INS)
Applying INS 
this simplifies to: 
R1.1 :  

{-1}  FORALL (l: LTL): (TRUE => l) => SQT(G(l))
[-2]  l2!1 => (l1!1 => l2!1)
[-3]  SQT(G(l2!1))
  |-------
[1]   SQT(G(l1!1 => l2!1))

Rerunning step: (inst -1 " l2!1 => (l1!1 => l2!1)")
Instantiating the top quantifier in -1 with the terms: 
  l2!1 => (l1!1 => l2!1),
this simplifies to: 
R1.1 :  

{-1}  (l2!1 => (l1!1 => l2!1)) => SQT(G(l2!1 => (l1!1 => l2!1)))
[-2]  l2!1 => (l1!1 => l2!1)
[-3]  SQT(G(l2!1))
  |-------
[1]   SQT(G(l1!1 => l2!1))

Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
R1.1.1 :  

{-1}  SQT(G(l2!1 => (l1!1 => l2!1)))
[-2]  l2!1 => (l1!1 => l2!1)
[-3]  SQT(G(l2!1))
  |-------
[1]   SQT(G(l1!1 => l2!1))

Rerunning step: (hide -2)
Hiding formulas:  -2,
this simplifies to: 
R1.1.1 :  

[-1]  SQT(G(l2!1 => (l1!1 => l2!1)))
[-2]  SQT(G(l2!1))
  |-------
[1]   SQT(G(l1!1 => l2!1))

Rerunning step: (forward-chain E_MP_01)
Forward chaining on E_MP_01,

This completes the proof of R1.1.1.

R1.1.2 :  

[-1]  l2!1 => (l1!1 => l2!1)
[-2]  SQT(G(l2!1))
  |-------
{1}   l2!1 => (l1!1 => l2!1)
[2]   SQT(G(l1!1 => l2!1))

which is trivially true.

This completes the proof of R1.1.2.


This completes the proof of R1.1.

R1.2 :  

[-1]  SQT(G(l2!1))
  |-------
{1}   l2!1 => (l1!1 => l2!1)
[2]   SQT(G(l1!1 => l2!1))

Rerunning step: (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of R1.2.

Q.E.D.
```

####some theorems

```
 %%% computational induction
    Rci	     :	lemma SQT(G(l => X(l))) => SQT(G(l => G(l)))
    %%% X generalization
    RXgen    :	lemma SQT(G(l)) => SQT(G(X(l)))
    %%% X is monotonic
    RXmon    :	lemma SQT(G(l1 => l2)) => SQT(G(X(l1) => X(l2)))

    T3	     :  theorem	  SQT(G(X(l1 and l2))) <=>SQT(G((X(l1) and X(l2))))
    T4	     :	theorem	  SQT(G(G(l) => X(G(l))))   

    %%% G generalization
    RGgen    :	lemma SQT(G(l)) => SQT(G(G(l)))
    %%% G is monotonic
    RGmon    :	lemma SQT(G(l1 => l2)) => SQT(G(G(l1) => G(l2)))

    T5	     :	theorem	 SQT(G(G(l1) or G(l2) => G(l1 or l2)))
    T6	     :	theorem	  SQT(G(l1 and l2)) <=>  SQT(G(l1) and G(l2))
```

####Rci-proof

![image-20201218172014472](LTL证明6.assets\image-20201218172014472.png)

```
Rci :  

  |-------
{1}   FORALL (l: LTL): SQT(G(l => X(l))) => SQT(G(l => G(l)))

Rerunning step: (skolem!)
Skolemizing,
this simplifies to: 
Rci :  

  |-------
{1}   SQT(G(l!1 => X(l!1))) => SQT(G(l!1 => G(l!1)))

Rerunning step: (flatten)
Applying disjunctive simplification to flatten sequent,
this simplifies to: 
Rci :  

{-1}  SQT(G(l!1 => X(l!1)))
  |-------
{1}   SQT(G(l!1 => G(l!1)))

Rerunning step: (lemma F5)
Applying F5 
this simplifies to: 
Rci :  

{-1}  FORALL (l: LTL): SQT(G(l => X(l)) => G(l => G(l)))
[-2]  SQT(G(l!1 => X(l!1)))
  |-------
[1]   SQT(G(l!1 => G(l!1)))

Rerunning step: (inst?)
Found substitution:
l: LTL gets l!1,
Using template: G(l => G(l))
Instantiating quantified variables,
this simplifies to: 
Rci :  

{-1}  SQT(G(l!1 => X(l!1)) => G(l!1 => G(l!1)))
[-2]  SQT(G(l!1 => X(l!1)))
  |-------
[1]   SQT(G(l!1 => G(l!1)))

Rerunning step: (forward-chain MP)
Forward chaining on MP,
Q.E.D.
```

####T3-proof

![image-20201218172004746](LTL证明6.assets\image-20201218172004746.png)

```
T3 :  

  |-------
{1}   FORALL (l1, l2: LTL):
        SQT(G(X(l1 AND l2))) <=> SQT(G((X(l1) AND X(l2))))

Rerunning step: (skolem!)
Skolemizing,
this simplifies to: 
T3 :  

  |-------
{1}   SQT(G(X(l1!1 AND l2!1))) <=> SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
```

#####<=

!![image-20201218171947689](LTL证明6.assets\image-20201218171947689.png)

```
T3.1 :  

  |-------
{1}   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (case "l1!1 and l2!1 => l1!1")
Case splitting on 
   l1!1 AND l2!1 => l1!1, 
this yields  2 subgoals: 
T3.1.1 :  

{-1}  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (lemma INS)
Applying INS 
this simplifies to: 
T3.1.1 :  

{-1}  FORALL (l: LTL): (TRUE => l) => SQT(G(l))
[-2]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (inst -1 "l1!1 AND l2!1 => l1!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 AND l2!1 => l1!1,
this simplifies to: 
T3.1.1 :  

{-1}  (l1!1 AND l2!1 => l1!1) => SQT(G(l1!1 AND l2!1 => l1!1))
[-2]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (lemma RXmon)
Applying RXmon 
this simplifies to: 
T3.1.1 :  

{-1}  FORALL (l1, l2: LTL): SQT(G(l1 => l2)) => SQT(G(X(l1) => X(l2)))
[-2]  (l1!1 AND l2!1 => l1!1) => SQT(G(l1!1 AND l2!1 => l1!1))
[-3]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (inst -1 "l1!1 and l2!1" "l1!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 and l2!1, l1!1,
this simplifies to: 
T3.1.1 :  

{-1}  SQT(G(l1!1 AND l2!1 => l1!1)) => SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-2]  (l1!1 AND l2!1 => l1!1) => SQT(G(l1!1 AND l2!1 => l1!1))
[-3]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
T3.1.1.1 :  

{-1}  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-2]  (l1!1 AND l2!1 => l1!1) => SQT(G(l1!1 AND l2!1 => l1!1))
[-3]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
T3.1.1.1.1 :  

{-1}  SQT(G(l1!1 AND l2!1 => l1!1))
[-2]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-3]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (case "l1!1 AND l2!1 => l2!1")
Case splitting on 
   l1!1 AND l2!1 => l2!1, 
this yields  2 subgoals: 
T3.1.1.1.1.1 :  

{-1}  l1!1 AND l2!1 => l2!1
[-2]  SQT(G(l1!1 AND l2!1 => l1!1))
[-3]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-4]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (lemma INS)
Applying INS 
this simplifies to: 
T3.1.1.1.1.1 :  

{-1}  FORALL (l: LTL): (TRUE => l) => SQT(G(l))
[-2]  l1!1 AND l2!1 => l2!1
[-3]  SQT(G(l1!1 AND l2!1 => l1!1))
[-4]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-5]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (inst -1 "l1!1 AND l2!1 => l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 AND l2!1 => l2!1,
this simplifies to: 
T3.1.1.1.1.1 :  

{-1}  (l1!1 AND l2!1 => l2!1) => SQT(G(l1!1 AND l2!1 => l2!1))
[-2]  l1!1 AND l2!1 => l2!1
[-3]  SQT(G(l1!1 AND l2!1 => l1!1))
[-4]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-5]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (lemma RXmon)
Applying RXmon 
this simplifies to: 
T3.1.1.1.1.1 :  

{-1}  FORALL (l1, l2: LTL): SQT(G(l1 => l2)) => SQT(G(X(l1) => X(l2)))
[-2]  (l1!1 AND l2!1 => l2!1) => SQT(G(l1!1 AND l2!1 => l2!1))
[-3]  l1!1 AND l2!1 => l2!1
[-4]  SQT(G(l1!1 AND l2!1 => l1!1))
[-5]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-6]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (inst -1 "l1!1 and l2!1" "l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 and l2!1, l2!1,
this simplifies to: 
T3.1.1.1.1.1 :  

{-1}  SQT(G(l1!1 AND l2!1 => l2!1)) => SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-2]  (l1!1 AND l2!1 => l2!1) => SQT(G(l1!1 AND l2!1 => l2!1))
[-3]  l1!1 AND l2!1 => l2!1
[-4]  SQT(G(l1!1 AND l2!1 => l1!1))
[-5]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-6]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
T3.1.1.1.1.1.1 :  

{-1}  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-2]  (l1!1 AND l2!1 => l2!1) => SQT(G(l1!1 AND l2!1 => l2!1))
[-3]  l1!1 AND l2!1 => l2!1
[-4]  SQT(G(l1!1 AND l2!1 => l1!1))
[-5]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-6]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
T3.1.1.1.1.1.1.1 :  

{-1}  SQT(G(l1!1 AND l2!1 => l2!1))
[-2]  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-3]  l1!1 AND l2!1 => l2!1
[-4]  SQT(G(l1!1 AND l2!1 => l1!1))
[-5]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-6]  l1!1 AND l2!1 => l1!1
  |-------
[1]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (forward-chain R5)
Forward chaining on R5,
this simplifies to: 
T3.1.1.1.1.1.1.1 :  

{-1}  SQT(G(l1!1 AND l2!1 => l2!1 AND l2!1))
[-2]  SQT(G(l1!1 AND l2!1 => l2!1))
[-3]  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-4]  l1!1 AND l2!1 => l2!1
[-5]  SQT(G(l1!1 AND l2!1 => l1!1))
[-6]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-7]  l1!1 AND l2!1 => l1!1
{-8}  SQT(G(X(l1!1 AND l2!1)))
  |-------
{1}   SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (lemma R5)
Applying R5 
this simplifies to: 
T3.1.1.1.1.1.1.1 :  

{-1}  FORALL (l, l1, l2: LTL):
        SQT(G(l => l1)) AND SQT(G(l => l2)) => SQT(G(l => l1 AND l2))
[-2]  SQT(G(l1!1 AND l2!1 => l2!1 AND l2!1))
[-3]  SQT(G(l1!1 AND l2!1 => l2!1))
[-4]  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-5]  l1!1 AND l2!1 => l2!1
[-6]  SQT(G(l1!1 AND l2!1 => l1!1))
[-7]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-8]  l1!1 AND l2!1 => l1!1
[-9]  SQT(G(X(l1!1 AND l2!1)))
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (inst -1 "X(l1!1 AND l2!1)" "X(l1!1)" "X(l2!1)")
Instantiating the top quantifier in -1 with the terms: 
 X(l1!1 AND l2!1), X(l1!1), X(l2!1),
this simplifies to: 
T3.1.1.1.1.1.1.1 :  

{-1}  SQT(G(X(l1!1 AND l2!1) => X(l1!1))) AND
       SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
       => SQT(G(X(l1!1 AND l2!1) => X(l1!1) AND X(l2!1)))
[-2]  SQT(G(l1!1 AND l2!1 => l2!1 AND l2!1))
[-3]  SQT(G(l1!1 AND l2!1 => l2!1))
[-4]  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-5]  l1!1 AND l2!1 => l2!1
[-6]  SQT(G(l1!1 AND l2!1 => l1!1))
[-7]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-8]  l1!1 AND l2!1 => l1!1
[-9]  SQT(G(X(l1!1 AND l2!1)))
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (split)
Splitting conjunctions,
this yields  3 subgoals: 
T3.1.1.1.1.1.1.1.1 :  

{-1}  SQT(G(X(l1!1 AND l2!1) => X(l1!1) AND X(l2!1)))
[-2]  SQT(G(l1!1 AND l2!1 => l2!1 AND l2!1))
[-3]  SQT(G(l1!1 AND l2!1 => l2!1))
[-4]  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-5]  l1!1 AND l2!1 => l2!1
[-6]  SQT(G(l1!1 AND l2!1 => l1!1))
[-7]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-8]  l1!1 AND l2!1 => l1!1
[-9]  SQT(G(X(l1!1 AND l2!1)))
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (forward-chain E_MP_01)
Forward chaining on E_MP_01,

This completes the proof of T3.1.1.1.1.1.1.1.1.

T3.1.1.1.1.1.1.1.2 :  

[-1]  SQT(G(l1!1 AND l2!1 => l2!1 AND l2!1))
[-2]  SQT(G(l1!1 AND l2!1 => l2!1))
[-3]  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-4]  l1!1 AND l2!1 => l2!1
[-5]  SQT(G(l1!1 AND l2!1 => l1!1))
[-6]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-7]  l1!1 AND l2!1 => l1!1
[-8]  SQT(G(X(l1!1 AND l2!1)))
  |-------
{1}   SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[2]   SQT(G((X(l1!1) AND X(l2!1))))

which is trivially true.

This completes the proof of T3.1.1.1.1.1.1.1.2.

T3.1.1.1.1.1.1.1.3 :  

[-1]  SQT(G(l1!1 AND l2!1 => l2!1 AND l2!1))
[-2]  SQT(G(l1!1 AND l2!1 => l2!1))
[-3]  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-4]  l1!1 AND l2!1 => l2!1
[-5]  SQT(G(l1!1 AND l2!1 => l1!1))
[-6]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-7]  l1!1 AND l2!1 => l1!1
[-8]  SQT(G(X(l1!1 AND l2!1)))
  |-------
{1}   SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[2]   SQT(G((X(l1!1) AND X(l2!1))))

which is trivially true.

This completes the proof of T3.1.1.1.1.1.1.1.3.


This completes the proof of T3.1.1.1.1.1.1.1.

T3.1.1.1.1.1.1.2 :  

[-1]  SQT(G(X(l1!1 AND l2!1) => X(l2!1)))
[-2]  l1!1 AND l2!1 => l2!1
[-3]  SQT(G(l1!1 AND l2!1 => l1!1))
[-4]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-5]  l1!1 AND l2!1 => l1!1
  |-------
{1}   l1!1 AND l2!1 => l2!1
[2]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

which is trivially true.

This completes the proof of T3.1.1.1.1.1.1.2.


This completes the proof of T3.1.1.1.1.1.1.

T3.1.1.1.1.1.2 :  

[-1]  (l1!1 AND l2!1 => l2!1) => SQT(G(l1!1 AND l2!1 => l2!1))
[-2]  l1!1 AND l2!1 => l2!1
[-3]  SQT(G(l1!1 AND l2!1 => l1!1))
[-4]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-5]  l1!1 AND l2!1 => l1!1
  |-------
{1}   SQT(G(l1!1 AND l2!1 => l2!1))
[2]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.1.1.1.1.1.2.


This completes the proof of T3.1.1.1.1.1.

T3.1.1.1.1.2 :  

[-1]  SQT(G(l1!1 AND l2!1 => l1!1))
[-2]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-3]  l1!1 AND l2!1 => l1!1
  |-------
{1}   l1!1 AND l2!1 => l2!1
[2]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.1.1.1.1.2.


This completes the proof of T3.1.1.1.1.

T3.1.1.1.2 :  

[-1]  SQT(G(X(l1!1 AND l2!1) => X(l1!1)))
[-2]  l1!1 AND l2!1 => l1!1
  |-------
{1}   l1!1 AND l2!1 => l1!1
[2]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

which is trivially true.

This completes the proof of T3.1.1.1.2.


This completes the proof of T3.1.1.1.

T3.1.1.2 :  

[-1]  (l1!1 AND l2!1 => l1!1) => SQT(G(l1!1 AND l2!1 => l1!1))
[-2]  l1!1 AND l2!1 => l1!1
  |-------
{1}   SQT(G(l1!1 AND l2!1 => l1!1))
[2]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))
 
Rerunning step: (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.1.1.2.


This completes the proof of T3.1.1.

T3.1.2 :  

  |-------
{1}   l1!1 AND l2!1 => l1!1
[2]   SQT(G(X(l1!1 AND l2!1))) IMPLIES SQT(G((X(l1!1) AND X(l2!1))))

Rerunning step: (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.1.2.


This completes the proof of T3.1.
```

#####=>

![](LTL证明6.assets\image-20201218171933822.png)

```
T3.2 :  

  |-------
{1}   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (case "l1!1 => (l2!1 => l1!1 and l2!1)")
Case splitting on 
   l1!1 => (l2!1 => l1!1 AND l2!1), 
this yields  2 subgoals: 
T3.2.1 :  

{-1}  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (lemma INS)
Applying INS 
this simplifies to: 
T3.2.1 :  

{-1}  FORALL (l: LTL): (TRUE => l) => SQT(G(l))
[-2]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (inst -1 "l1!1 => (l2!1 => l1!1 AND l2!1)")
Instantiating the top quantifier in -1 with the terms: 
 l1!1 => (l2!1 => l1!1 AND l2!1),
this simplifies to: 
T3.2.1 :  

{-1}  (l1!1 => (l2!1 => l1!1 AND l2!1)) =>
       SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-2]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
T3.2.1.1 :  

{-1}  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-2]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (lemma RXmon)
Applying RXmon 
this simplifies to: 
T3.2.1.1 :  

{-1}  FORALL (l1, l2: LTL): SQT(G(l1 => l2)) => SQT(G(X(l1) => X(l2)))
[-2]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-3]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (inst -1 "l1!1" "l2!1 => l1!1 AND l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l1!1, l2!1 => l1!1 AND l2!1,
this simplifies to: 
T3.2.1.1 :  

{-1}  SQT(G(l1!1 => l2!1 => l1!1 AND l2!1)) =>
       SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-2]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-3]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (split)
Splitting conjunctions,
this yields  2 subgoals: 
T3.2.1.1.1 :  

{-1}  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-2]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-3]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (lemma F2)
Applying F2 
this simplifies to: 
T3.2.1.1.1 :  

{-1}  FORALL (l1, l2: LTL): SQT(G(X(l1 => l2) => X(l1) => X(l2)))
[-2]  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-3]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-4]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (inst -1 "l2!1" "l1!1 and l2!1")
Instantiating the top quantifier in -1 with the terms: 
 l2!1, l1!1 and l2!1,
this simplifies to: 
T3.2.1.1.1 :  

{-1}  SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-2]  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-3]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-4]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (lemma E_TRNS)
Applying E_TRNS 
this simplifies to: 
T3.2.1.1.1 :  

{-1}  FORALL (l, l1, l2: LTL):
        SQT(G(l1 => l2)) AND SQT(G(l2 => l)) => SQT(G(l1 => l))
[-2]  SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-3]  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-4]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-5]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (inst -1 "X(l2!1) => X(l1!1 AND l2!1)" "X(l1!1)"
                 "X(l2!1 => l1!1 AND l2!1)")
Instantiating the top quantifier in -1 with the terms: 
 X(l2!1) => X(l1!1 AND l2!1), X(l1!1), X(l2!1 => l1!1 AND l2!1),
this simplifies to: 
T3.2.1.1.1 :  

{-1}  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1))) AND
       SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
       => SQT(G(X(l1!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-2]  SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-3]  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-4]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-5]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (split)
Splitting conjunctions,
this yields  3 subgoals: 
T3.2.1.1.1.1 :  

{-1}  SQT(G(X(l1!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-2]  SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-3]  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-4]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-5]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
[1]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (forward-chain R7)
Forward chaining on R7,
this simplifies to: 
T3.2.1.1.1.1 :  

{-1}  SQT(G(X(l1!1) AND X(l2!1) => X(l1!1 AND l2!1)))
[-2]  SQT(G(X(l1!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-3]  SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-4]  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-5]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-6]  l1!1 => (l2!1 => l1!1 AND l2!1)
{-7}  SQT(G((X(l1!1) AND X(l2!1))))
  |-------
{1}   SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (forward-chain E_MP_01)
Forward chaining on E_MP_01,

This completes the proof of T3.2.1.1.1.1.

T3.2.1.1.1.2 :  

[-1]  SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-2]  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-3]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-4]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
{1}   SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[2]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

which is trivially true.

This completes the proof of T3.2.1.1.1.2.

T3.2.1.1.1.3 :  

[-1]  SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
[-2]  SQT(G(X(l1!1) => X(l2!1 => l1!1 AND l2!1)))
[-3]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-4]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
{1}   SQT(G(X(l2!1 => l1!1 AND l2!1) => X(l2!1) => X(l1!1 AND l2!1)))
[2]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

which is trivially true.

This completes the proof of T3.2.1.1.1.3.


This completes the proof of T3.2.1.1.1.

T3.2.1.1.2 :  

[-1]  SQT(G(l1!1 => (l2!1 => l1!1 AND l2!1)))
[-2]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
{1}   SQT(G(l1!1 => l2!1 => l1!1 AND l2!1))
[2]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

which is trivially true.

This completes the proof of T3.2.1.1.2.


This completes the proof of T3.2.1.1.

T3.2.1.2 :  

[-1]  l1!1 => (l2!1 => l1!1 AND l2!1)
  |-------
{1}   l1!1 => (l2!1 => l1!1 AND l2!1)
[2]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

which is trivially true.

This completes the proof of T3.2.1.2.


This completes the proof of T3.2.1.

T3.2.2 :  

  |-------
{1}   l1!1 => (l2!1 => l1!1 AND l2!1)
[2]   SQT(G((X(l1!1) AND X(l2!1)))) IMPLIES SQT(G(X(l1!1 AND l2!1)))

Rerunning step: (grind)
Trying repeated skolemization, instantiation, and if-lifting,

This completes the proof of T3.2.2.


This completes the proof of T3.2.

Q.E.D.
```

####T4

![](LTL证明6.assets\image-20201218171924620.png)

```

```





- 语义后承（semantic consequence），符号是![\models](https://www.zhihu.com/equation?tex=%5Cmodels)（\models）。语义后承在一般情况下是连接一个命题集合和一个命题。如果，在任何一种语义赋值下，只要命题集合![\Sigma](https://www.zhihu.com/equation?tex=%5CSigma)中的每一个命题都为真，那么![\phi](https://www.zhihu.com/equation?tex=%5Cphi)就一定为真，那么，我们就说![\phi](https://www.zhihu.com/equation?tex=%5Cphi)是![\Sigma](https://www.zhihu.com/equation?tex=%5CSigma)的语义后承，记作![\Sigma\models \phi](https://www.zhihu.com/equation?tex=%5CSigma%5Cmodels+%5Cphi)。

- 句法后承（syntactic consequence），符号是![\vdash](https://www.zhihu.com/equation?tex=%5Cvdash)（\vdash）。句法后承的用法和语义后承类似，也是连接一个命题集合和一个命题，如![\Sigma \vdash\phi](https://www.zhihu.com/equation?tex=%5CSigma+%5Cvdash%5Cphi)，表示的是![\phi](https://www.zhihu.com/equation?tex=%5Cphi)可以通过**句法证明**的方式从命题集![\Sigma](https://www.zhihu.com/equation?tex=%5CSigma)中得出。即，存在一个证明，使得每个前提要么是公理，要么是![\Sigma](https://www.zhihu.com/equation?tex=%5CSigma)中的命题，而证明的结论是![\phi](https://www.zhihu.com/equation?tex=%5Cphi)。具体来说，一个证明是一个命题序列，其中每个命题要么是公理，要么是前提，要么是由前面的命题通过证明规则得到的。其中最后一个称为结论。
- 实质蕴含（material implication / material conditional），符号是![\rightarrow](https://www.zhihu.com/equation?tex=%5Crightarrow)（\rightarrow） 。实质蕴含是一个命题逻辑中的二元算子，连接的是两个命题。在句法系统中，由 Hilbert 的前两条公理完全刻画，由第三条公理刻画它和否定的关系。[1] 在语义系统中， 我们说![\Sigma\models p\rightarrow q](https://www.zhihu.com/equation?tex=%5CSigma%5Cmodels+p%5Crightarrow+q)当且仅当![\Sigma\models q](https://www.zhihu.com/equation?tex=%5CSigma%5Cmodels+q)或者![\Sigma\models\neg p](https://www.zhihu.com/equation?tex=%5CSigma%5Cmodels%5Cneg+p)。就是说，如果一个实质蕴含条件句成立，就是说，前件（上面的 p）为真的情况下，后件（上面的 q）不可能为假。[2]



Hilbert 的公理系统可以写作三个公理模式加一个规则：
![[公式]](https://www.zhihu.com/equation?tex=%28%5Cmathcal%7BA%7D%5Crightarrow%28%5Cmathcal%7BB%7D%5Crightarrow%5Cmathcal%7BA%7D%29%29)
![[公式]](https://www.zhihu.com/equation?tex=%28%28%5Cmathcal%7BA%7D%5Crightarrow%28%5Cmathcal%7BB%7D%5Crightarrow+%5Cmathcal%7BC%7D%29%29%5Crightarrow%28%28%5Cmathcal%7BA%7D%5Crightarrow+%5Cmathcal%7BB%7D%29%5Crightarrow%28%5Cmathcal%7BA%7D%5Crightarrow%5Cmathcal%7BC%7D%29%29%29)
![[公式]](https://www.zhihu.com/equation?tex=%28%28%28%5Cneg%5Cmathcal%7BA%7D%29%5Crightarrow%28%5Cneg%5Cmathcal%7BB%7D%29%29%5Crightarrow%28%5Cmathcal%7BB%7D%5Crightarrow%5Cmathcal%7BA%7D%29%29)
为什么说是公理模式呢？因为这里的![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal%7BA%7D)、![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal%7BB%7D)、![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal%7BC%7D)都是元语言中用来代表合式公式的符号。换而言之，这个系统中有无穷多条公理。
至于推理规则，就只有一个 MP 规则：![[公式]](https://www.zhihu.com/equation?tex=%5Cphi%2C%5Cphi%5Crightarrow%5Cpsi%5Cvdash%5Cpsi)，中间的![[公式]](https://www.zhihu.com/equation?tex=%5Cvdash)表示证明系统中的推出，并且，这里的![[公式]](https://www.zhihu.com/equation?tex=%5Cphi)和![[公式]](https://www.zhihu.com/equation?tex=%5Cpsi)也都是元语言中代表合式公式的符号。

这种情况下，如果我们要证明![[公式]](https://www.zhihu.com/equation?tex=%5Cemptyset%5Cvdash+p%5Crightarrow+p)（从空集出发能够推出，即表示在系统内可证），那么我们要写出如下命题序列：

1. ![[公式]](https://www.zhihu.com/equation?tex=p%5Crightarrow%28%28p%5Crightarrow+p%29%5Crightarrow+p%29)（公理 1）
2. ![[公式]](https://www.zhihu.com/equation?tex=%28+p%5Crightarrow%28+%28p%5Crightarrow+p%29%5Crightarrow+p+%29%29%5Crightarrow%28%28p%5Crightarrow+%28p+%5Crightarrow+p%29%29%5Crightarrow%28p%5Crightarrow+p%29%29)（公理 2）
3. ![[公式]](https://www.zhihu.com/equation?tex=+%28%28p%5Crightarrow+%28p+%5Crightarrow+p%29%29%5Crightarrow%28p%5Crightarrow+p%29%29)（1、2，MP 规则） 
4. ![[公式]](https://www.zhihu.com/equation?tex=+p%5Crightarrow+%28p+%5Crightarrow+p%29)（公理 1）
5. ![[公式]](https://www.zhihu.com/equation?tex=p+%5Crightarrow+p)（4、3，MP 规则） 

[2] 但是在语义系统中，如果我们要说明 ![[公式]](https://www.zhihu.com/equation?tex=%5Cemptyset%5Cmodels+p%5Crightarrow+p)（即，是空命题集的语义后承，或者说，是永真的），那么我们只需要说明，根据实质蕴含算子的语义规则，对于任何满足了空空命题集的模型 ![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal+M) （也即，对于任何模型），都有![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal+M%5CVdash+p%5Crightarrow+p)，而根据定义，这等价于![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal+M%5CVdash++p) 或者![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal+M%5Cnot%5CVdash++p) ——这是一个总是能达到的条件。因此，我们会说这个公式是空集的语义后承。

一般来说，命题逻辑中的语义规则可以简单地给出：

- ![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal+M%5CVdash+p) （对于原子命题 p）当且仅当 ![[公式]](https://www.zhihu.com/equation?tex=+V%28p%29%3D1) （ ![[公式]](https://www.zhihu.com/equation?tex=V) 由模型给出的，是从所有原子命题的集合到真假的函数，一般来说命题逻辑的模型只需要确定这个函数就足够了）；
- ![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal+M%5CVdash+%5Cneg%5Cphi%5Ciff%5Cmathcal+M%5Cnot%5CVdash%5Cphi) 。这个操作看上去是循环，但是考虑到 ![[公式]](https://www.zhihu.com/equation?tex=%5Cphi) 总是比 ![[公式]](https://www.zhihu.com/equation?tex=%5Cneg+%5Cphi) 更加简单，所以我们在确定公式真假的时候，总是先确定我们的模型中 ![[公式]](https://www.zhihu.com/equation?tex=%5Cphi) 能不能被满足，如果能，那么 ![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal+M%5Cnot%5CVdash%5Cneg%5Cphi) ，如果不能，那么 ![[公式]](https://www.zhihu.com/equation?tex=%5Cmathcal+M%5CVdash+%5Cneg%5Cphi) 。

[3] 强完全性：对于任意的公式集合![[公式]](https://www.zhihu.com/equation?tex=%5CSigma)，对于任意的公式![[公式]](https://www.zhihu.com/equation?tex=%5Cphi)，如果![[公式]](https://www.zhihu.com/equation?tex=%5CSigma%5Cmodels+%5Cphi)，那么![[公式]](https://www.zhihu.com/equation?tex=%5CSigma+%5Cvdash%5Cphi) 。强可靠性：

对于任意的公式集合![[公式]](https://www.zhihu.com/equation?tex=%5CSigma)，对于任意的公式![[公式]](https://www.zhihu.com/equation?tex=%5Cphi)，如果![[公式]](https://www.zhihu.com/equation?tex=%5CSigma+%5Cvdash%5Cphi)，那么![[公式]](https://www.zhihu.com/equation?tex=%5CSigma%5Cmodels+%5Cphi)。而弱完全性是，方式为真的公式都是可证的；弱可靠性是，凡是可证的公式都是为真的（事实上我们会这样认为，如果一个公理系统只有弱完全性，那么它还是好的系统，但是如果一个语义系统被弄成只有弱可靠性，那肯定是哪里出了问题）。 在有完全性和可靠性的基础上，没有必要在实际运用中区分两种推出。





演绎定理

![image-20201218171916662](LTL证明6.assets\image-20201218171916662.png)









###### 添加

```
LTL_test6  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

  
    LTL: nonempty_type = boolean

    p,q,r      : var bool
    l,l1,l2    : var LTL

    tt		  : LTL = true;
    ff		  : LTL = not tt;

    X:  [LTL -> LTL];
    F:  [LTL -> LTL];
    G:  [LTL -> LTL];
    U:  [LTL , LTL -> LTL];
    W:  [LTL , LTL -> LTL];
    R:  [LTL , LTL -> LTL];

    A1 : axiom F(F(l)) => F(l)
    A2 : axiom G(l) => l;

 

    Gamma	  : var LTL;
    Delta	  : var LTL;
    
    emptyconsequent : LTL = ff;
    emptyantecedent : LTL = tt;

    X		  : var LTL;
    *(Gamma , X)  : LTL = Gamma and X;
    +(Delta , X)  : LTL = Delta or X;
    
    SQT(Gamma , Delta) : bool = Gamma => Delta;       


    |-(Delta) : bool = SQT(emptyantecedent , Delta);
  
    MP : axiom |-(l1) and |-(l1 => l2)  => |-(l2);
   

    Rule_G:			lemma |-(l) => |-(G(l))
    Rule_F_impl     : lemma |-(l1 => l2) => |-(F(l1) => F(l2))
    Rule_G_impl	    : lemma |-(l1 => l2) => |-(G(l1) => G(l2))


    Th1:	    lemma  G(l1) and F(l2) => F(l1 and l2)
    %Th1_1:	    lemma |-(G(l1) and F(l2) => F(l1 and l2))

    Th2: 	    lemma G(l1 and l2) <=> G(l1) and G(l2)
    Th3:	    lemma F(l1 and l2) => F(l1) and F(l2)
    Th4:	    lemma F(l1 or l2) <=> F(l1) or F(l2)
    Th5:	    lemma F(G(l)) => G(F(l))
    Th6:	    lemma F(l) <=> F(F(l))
    Th7:	    lemma G(G(l)) <=> G(l)


    STL1:	    lemma  |-(l) => |-(G(l))
    STL2:	    lemma G(l) => l
    STL3:	    lemma G(G(l)) <=> G(l)
    STL4:	    lemma |-(l1 => l2) => |-(G(l1) => G(l2))

    STL5:	    lemma G(l1 and l2) <=> G(l1) and G(l2)


   
    STL7:	    lemma G(F(G(l))) <=> F(G(l))
    STL6:	    lemma F(G(l1)) and F(G(l2)) <=> F(G(l1 and l2))
   
  END LTL_test6 
```



## |-  探究

```shell
DCP  
		: THEORY

  BEGIN

  % ASSUMING
  % assuming declarations
  % ENDASSUMING


	%%% NL syntax   Neighbourhood Logic
	Time : TYPE = real;
	Interval : type = {b:Time , e:Time | e >= b};

	GV: type+      %%global variables
	gx: GV

	TV: type+  %%temporal variables
	tv: TV

	Value: type = real
	GV_value : type = [GV -> Value]
	TV_value : type = [TV -> [Interval -> Value]];

	Sem : type = [GV_value , TV_value , Interval];

	i , j : var Interval;
	r , rx : var Value;
	sem : var Sem;

	Term : type = [Sem -> Value];
	Form : type = [Sem -> bool];
	t1 ,t2 : var Term
	A , B , C: var Form
	+(t1,t2): Term = (lambda sem : t1(sem) + t2(sem));
	>=(t1,t2): Form = (lambda (sem:Sem):t1(sem) >= t2(sem));

	not(A): Form = lambda sem : not A(sem);
	\/(A,B):Form = lambda sem : A(sem) or B(sem);

	=>(A,B):Form =  not A \/ B;
	
	tt : Form = lambda sem: 0=0;

	X(A): Form
	G(A): Form
	F(A): Form
	^(A,B): Form

	satisfiable(A) : bool = exists sem: A(sem);
	|-(A)	       : bool = forall sem: A(sem);


	distribute : axiom |- (F(A \/ B) => (F(A) \/ F(B)));
	MP	   : lemma |-(A => B) and |-(A) => |-(B);


  END DCP
```

