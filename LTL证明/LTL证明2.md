##LTL语法

$$
\phi::= \top |\bot|p|(\urcorner \phi)|(\phi \wedge \phi)|(\phi \vee \phi)|
(\phi \rightarrow \phi)
$$

$$
\quad\quad(X \phi)|(F \phi)|(G \phi)|(\phi U \phi)|(\phi W \phi)|(\phi R \phi)
$$

### datatype表示

```
  LTL: datatype
    begin
        true			: true?
        atom(p:P)		: atom?
        -(l1:LTL)		: not?
        and(l1,l2: LTL)	: and?

        X(l1:LTL)  		: next?
        F(l1: LTL)		: future?
        U(l1,l2:LTL)	: until?
    end LTL
```

### 常用等价公式

```
  l1,l2      : var LTL 
  false      : LTL = -true
  or_(l1,l2) : LTL = -(-l1 and -l2)
  impl(l1,l2): LTL = or_(-l1,l2)
  G(l1)      : LTL = -F(-l1)
  W(l1,l2)   : LTL = or_ (U(l1,l2),G(l1))
  R(l1,l2)   : LTL = W(l2,l1 and l2)
```

### 公理axiom

```
  not_X       : axiom - X(l1) = X(-l1)
  not_and     : axiom -(l1 and l2) = or_(-l1,-l2)  %lemma
  not_elim    : axiom --l1 = l1
  G_X_swap    : axiom X(G(l1)) = G(X(l1))
  F_equal_T_U : axiom F(l1) = U(true,l1)
```

### 引理（待证明）lemma

```
  not_or   : lemma (-(or_(l1,l2))) = and(-l1,-l2)
  F2G      : lemma -F(l1) = G(-l1)    %  =>
  G2F      : lemma -G(l1) = F(-l1)
  U2R      : lemma -(U(l1,l2)) = R(-l1,-l2)
```

> not_or

+ ```
  not_or :  
  
    |-------
  {1}   FORALL (l1, l2: LTL): (-(or_(l1, l2))) = (-l1 AND -l2)
  
  Rule? (skosimp)
  Skolemizing and flattening,
  this simplifies to: 
  not_or :  
  
    |-------
  {1}   (-(or_(l1!1, l2!1))) = (-l1!1 AND -l2!1)
  
  Rule? (grind)
  or_ rewrites (or_(l1!1, l2!1))
    to -(-l1!1 AND -l2!1)
  Trying repeated skolemization, instantiation, and if-lifting,
  this simplifies to: 
  not_or :  
  
    |-------
  {1}   (--(-l1!1 AND -l2!1)) = (-l1!1 AND -l2!1)
  
  Rule? (lemma not_elim)
  Applying not_elim 
  this simplifies to: 
  not_or :  
  
  {-1}  FORALL (l1: LTL): --l1 = l1
    |-------
  [1]   (--(-l1!1 AND -l2!1)) = (-l1!1 AND -l2!1)
  
  Rule? (inst?)
  Found substitution:
  l1: LTL gets (-l1!1 AND -l2!1),
  Using template: --l1 = l1
  Instantiating quantified variables,
  Q.E.D.
  ```
  
  
```
  F_equal_T_U  : axiom F(l1) = U(true,l1)
  G_equal_F_R  : lemma G(l1) = R(false,l1)
  U2W_F        : lemma U(l1,l2) = and((W(l1,l2)), F(l2))

  %U2W_F       : lemma U(l1,l2) = (W(l1,l2)) and F(l2)
  %first argument to AND has the wrong type 
  %Found: booleans.boolean Expected:     LTL <=> LTL

  W2U_G        : lemma W(l1,l2) = or_(U(l1,l2),G(l1))

  W2R          : lemma W(l1,l2) = R(l2,or_(l1,l2))
  R2W          : lemma R(l1,l2) = W(l2,and(l1,l2))

```
$$
G \phi \equiv \bot R \phi
$$

```
  G_equal_F_R : 
  
    |-------
  {1}   FORALL (l1: LTL): G(l1) = R(FALSE, l1)
  
  Rule? (skosimp)
  Skolemizing and flattening,
  this simplifies to: 
  G_equal_F_R :  
  
    |-------
  {1}   G(l1!1) = R(FALSE, l1!1)
  
  Rule? (use F_equal_T_U)
  Using lemma F_equal_T_U,
  this simplifies to: 
  G_equal_F_R :  
  
  {-1}  F(G(l1!1)) = U(TRUE, G(l1!1))
    |-------
  [1]   G(l1!1) = R(FALSE, l1!1)
  
  Rule? (grind)
  G rewrites G(l1!1)
    to -F(-l1!1)
  Trying repeated skolemization, instantiation, and if-lifting,
  Q.E.D.
```

$$
\phi W \psi \equiv \phi U \psi \vee G\psi
$$

+ ```
  W2U_G :  
  
    |-------
  {1}   FORALL (l1, l2: LTL): W(l1, l2) = or_(U(l1, l2), G(l1))
  
  Rerunning step: (grind)
  G rewrites G(l1)
    to -F(-l1)
  or_ rewrites or_(U(l1, l2), -F(-l1))
    to -(-U(l1, l2) AND --F(-l1))
  W rewrites W(l1, l2)
    to -(-U(l1, l2) AND --F(-l1))
  Trying repeated skolemization, instantiation, and if-lifting,
  Q.E.D.
  ```

  $$
\phi R \psi \equiv \psi W (\phi \wedge \psi)
  $$
  
+ ```
  R2W :  
  
    |-------
  {1}   FORALL (l1, l2: LTL): R(l1, l2) = W(l2, (l1 AND l2))
  
  Rerunning step: (grind)
  G rewrites G(l2)
    to -F(-l2)
  or_ rewrites or_(U(l2, l1 AND l2), -F(-l2))
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  W rewrites W(l2, l1 AND l2)
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  R rewrites R(l1, l2)
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  Trying repeated skolemization, instantiation, and if-lifting,
  Q.E.D.
  ```

  
  
  
  
  ```
    Fnot_notG  : lemma F(-l1) = -G(l1)
    G_not_notF : lemma G(-l1) = -F(l1)
   
    U_double   : lemma U(l1,l2) = U(l1,U(l1,l2))
  
    Test01     : lemma U(l1,l2) = and(-(U(-l2,and(-l1,-l2))),F(l2))
  ```
  
  
  $$
  \phi U \psi  = \phi U (\phi U \psi)
  $$
  
  ```
  U_double :  
  
    |-------
  {1}   U(l1!1, l2!1) = U(l1!1, U(l1!1, l2!1))
  
  Rerunning step: (grind)
  Trying repeated skolemization, instantiation, and if-lifting,
  this simplifies to: 
  U_double :  
  
    |-------
  [1]   U(l1!1, l2!1) = U(l1!1, U(l1!1, l2!1))
  
  Rerunning step: (use U2R)
  Using lemma U2R,
  this simplifies to: 
  U_double :  
  
  {-1}  -(U(l1!1, l2!1)) = R(-l1!1, -l2!1)
    |-------
  [1]   U(l1!1, l2!1) = U(l1!1, U(l1!1, l2!1))
  
  Rerunning step: (grind)
  G rewrites G(-l2!1)
    to -F(--l2!1)
  or_ rewrites or_(U(-l2!1, -l1!1 AND -l2!1), -F(--l2!1))
    to -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1))
  W rewrites W(-l2!1, -l1!1 AND -l2!1)
    to -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1))
  R rewrites R(-l1!1, -l2!1)
    to -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1))
  Trying repeated skolemization, instantiation, and if-lifting,
  this simplifies to: 
  U_double :  
  
  {-1}  -(U(l1!1, l2!1)) = -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1))
    |-------
  [1]   U(l1!1, l2!1) = U(l1!1, U(l1!1, l2!1))
  
  Rerunning step: (use not_elim)
  Using lemma not_elim,
  this simplifies to: 
  U_double :  
  
  {-1}  --F(--l2!1) = F(--l2!1)
  [-2]  -(U(l1!1, l2!1)) = -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1))
    |-------
  [1]   U(l1!1, l2!1) = U(l1!1, U(l1!1, l2!1))
  
  Rerunning step: (use not_and)
  Using lemma not_and,
  this simplifies to: 
  U_double :  
  
  {-1}  -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1)) =
         or_(--U(-l2!1, -l1!1 AND -l2!1), ---F(--l2!1))
  [-2]  --F(--l2!1) = F(--l2!1)
  [-3]  -(U(l1!1, l2!1)) = -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1))
    |-------
  [1]   U(l1!1, l2!1) = U(l1!1, U(l1!1, l2!1))
  
  Rerunning step: (grind)
  or_ rewrites or_(--U(-l2!1, -l1!1 AND -l2!1), ---F(--l2!1))
    to -(---U(-l2!1, -l1!1 AND -l2!1) AND ----F(--l2!1))
  Trying repeated skolemization, instantiation, and if-lifting,
  Q.E.D.
  ```
  
  $$
  \begin{equation}
  \begin{split}
  \phi U \psi &\equiv \urcorner(\urcorner \psi U (\urcorner \phi \wedge \urcorner \psi)) \wedge F \psi \\
  &\Rightarrow \phi U \psi\equiv \urcorner (\urcorner \phi R \urcorner \psi) \\
  &\Rightarrow \phi U \psi\equiv \urcorner (\urcorner \psi W (\urcorner \phi \wedge \urcorner\psi ))\\
  &\Rightarrow \phi U \psi\equiv \urcorner (\urcorner \psi U (\urcorner \phi \wedge \urcorner\psi )\vee G \urcorner\psi)\\
  &\Rightarrow \phi U \psi\equiv \urcorner (\urcorner \psi U (\urcorner \phi \wedge \urcorner\psi ))\wedge F \psi
  \end{split}
  \end{equation}
  $$
  
  ```
  Test01 :  
  
    |-------
  {1}   FORALL (l1, l2: LTL): U(l1, l2) = (-(U(-l2, (-l1 AND -l2))) AND F(l2))
  
  Rule? (skosimp)
  Skolemizing and flattening,
  this simplifies to: 
  Test01 :  
  
    |-------
  {1}   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (lemma U2R)
  Applying U2R 
  this simplifies to: 
  Test01 :  
  
  {-1}  FORALL (l1, l2: LTL): -(U(l1, l2)) = R(-l1, -l2)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (inst -1 l1!1 l2!1)
  Instantiating the top quantifier in -1 with the terms: 
   l1!1, l2!1,
  this simplifies to: 
  Test01 :  
  
  {-1}  -(U(l1!1, l2!1)) = R(-l1!1, -l2!1)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (lemma R2W)
  Applying R2W 
  this simplifies to: 
  Test01 :  
  
  {-1}  FORALL (l1, l2: LTL): R(l1, l2) = W(l2, (l1 AND l2))
  [-2]  -(U(l1!1, l2!1)) = R(-l1!1, -l2!1)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (inst -1 -l1!1 -l2!1)
  Instantiating the top quantifier in -1 with the terms: 
   -l1!1, -l2!1,
  this simplifies to: 
  Test01 :  
  
  {-1}  R(-l1!1, -l2!1) = W(-l2!1, (-l1!1 AND -l2!1))
  [-2]  -(U(l1!1, l2!1)) = R(-l1!1, -l2!1)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (lemma W2U_G)
  Applying W2U_G 
  this simplifies to: 
  Test01 :  
  
  {-1}  FORALL (l1, l2: LTL): W(l1, l2) = or_(U(l1, l2), G(l1))
  [-2]  R(-l1!1, -l2!1) = W(-l2!1, (-l1!1 AND -l2!1))
  [-3]  -(U(l1!1, l2!1)) = R(-l1!1, -l2!1)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (inst -1 -l2!1 (-l1!1 AND -l2!1))
  Instantiating the top quantifier in -1 with the terms: 
   -l2!1, (-l1!1 AND -l2!1),
  this simplifies to: 
  Test01 :  
  
  {-1}  W(-l2!1, (-l1!1 AND -l2!1)) =
         or_(U(-l2!1, (-l1!1 AND -l2!1)), G(-l2!1))
  [-2]  R(-l1!1, -l2!1) = W(-l2!1, (-l1!1 AND -l2!1))
  [-3]  -(U(l1!1, l2!1)) = R(-l1!1, -l2!1)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (skosimp)
  No suitable (+ve FORALL/-ve EXISTS) quantified expression found.
  
  No change on: (skosimp)
  Test01 :  
  
  {-1}  W(-l2!1, (-l1!1 AND -l2!1)) =
         or_(U(-l2!1, (-l1!1 AND -l2!1)), G(-l2!1))
  [-2]  R(-l1!1, -l2!1) = W(-l2!1, (-l1!1 AND -l2!1))
  [-3]  -(U(l1!1, l2!1)) = R(-l1!1, -l2!1)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (grind)
  G rewrites G(-l2!1)
    to -F(--l2!1)
  or_ rewrites or_(U(-l2!1, (-l1!1 AND -l2!1)), -F(--l2!1))
    to -(-U(-l2!1, (-l1!1 AND -l2!1)) AND --F(--l2!1))
  W rewrites W(-l2!1, (-l1!1 AND -l2!1))
    to -(-U(-l2!1, (-l1!1 AND -l2!1)) AND --F(--l2!1))
  R rewrites R(-l1!1, -l2!1)
    to -(-U(-l2!1, (-l1!1 AND -l2!1)) AND --F(--l2!1))
  Trying repeated skolemization, instantiation, and if-lifting,
  this simplifies to: 
  Test01 :  
  
  {-1}  -(U(l1!1, l2!1)) = -(-U(-l2!1, (-l1!1 AND -l2!1)) AND --F(--l2!1))
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (use not_elim)
  Using lemma not_elim,
  this simplifies to: 
  Test01 :  
  
  {-1}  --F(--l2!1) = F(--l2!1)
  [-2]  -(U(l1!1, l2!1)) = -(-U(-l2!1, (-l1!1 AND -l2!1)) AND --F(--l2!1))
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rule? (grind)
  Trying repeated skolemization, instantiation, and if-lifting,
  Q.E.D.
  ```
  
  
  
  
  
  ```
  Test01 :  
  
    |-------
  {1}   FORALL (l1, l2: LTL): U(l1, l2) = (-(U(-l2, (-l1 AND -l2))) AND F(l2))
  
  Rerunning step: (grind)
  Trying repeated skolemization, instantiation, and if-lifting,
  this simplifies to: 
  Test01 :  
  
    |-------
  {1}   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rerunning step: (lemma U2R)
  Applying U2R 
  this simplifies to: 
  Test01 :  
  
  {-1}  FORALL (l1, l2: LTL): -(U(l1, l2)) = R(-l1, -l2)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rerunning step: (lemma R2W)
  Applying R2W 
  this simplifies to: 
  Test01 :  
  
  {-1}  FORALL (l1, l2: LTL): R(l1, l2) = W(l2, (l1 AND l2))
  [-2]  FORALL (l1, l2: LTL): -(U(l1, l2)) = R(-l1, -l2)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rerunning step: (lemma W2U_G)
  Applying W2U_G 
  this simplifies to: 
  Test01 :  
  
  {-1}  FORALL (l1, l2: LTL): W(l1, l2) = or_(U(l1, l2), G(l1))
  [-2]  FORALL (l1, l2: LTL): R(l1, l2) = W(l2, (l1 AND l2))
  [-3]  FORALL (l1, l2: LTL): -(U(l1, l2)) = R(-l1, -l2)
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rerunning step: (grind)
  G rewrites G(l1)
    to -F(-l1)
  or_ rewrites or_(U(l1, l2), -F(-l1))
    to -(-U(l1, l2) AND --F(-l1))
  W rewrites W(l1, l2)
    to -(-U(l1, l2) AND --F(-l1))
  G rewrites G(l2)
    to -F(-l2)
  or_ rewrites or_(U(l2, l1 AND l2), -F(-l2))
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  W rewrites W(l2, l1 AND l2)
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  R rewrites R(l1, l2)
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  G rewrites G(-l2)
    to -F(--l2)
  or_ rewrites or_(U(-l2, -l1 AND -l2), -F(--l2))
    to -(-U(-l2, -l1 AND -l2) AND --F(--l2))
  W rewrites W(-l2, -l1 AND -l2)
    to -(-U(-l2, -l1 AND -l2) AND --F(--l2))
  R rewrites R(-l1, -l2)
    to -(-U(-l2, -l1 AND -l2) AND --F(--l2))
  G rewrites G(l1)
    to -F(-l1)
  or_ rewrites or_(U(l1, l2), -F(-l1))
    to -(-U(l1, l2) AND --F(-l1))
  W rewrites W(l1, l2)
    to -(-U(l1, l2) AND --F(-l1))
  G rewrites G(l2)
    to -F(-l2)
  or_ rewrites or_(U(l2, l1 AND l2), -F(-l2))
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  W rewrites W(l2, l1 AND l2)
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  R rewrites R(l1, l2)
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  G rewrites G(l1!1)
    to -F(-l1!1)
  or_ rewrites or_(U(l1!1, l2!1), -F(-l1!1))
    to -(-U(l1!1, l2!1) AND --F(-l1!1))
  W rewrites W(l1!1, l2!1)
    to -(-U(l1!1, l2!1) AND --F(-l1!1))
  G rewrites G(l2)
    to -F(-l2)
  or_ rewrites or_(U(l2, l1 AND l2), -F(-l2))
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  W rewrites W(l2, l1 AND l2)
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  R rewrites R(l1, l2)
    to -(-U(l2, l1 AND l2) AND --F(-l2))
  G rewrites G(--F(-l1!1))
    to -F(---F(-l1!1))
  or_ rewrites 
    or_(U(--F(-l1!1), -U(l1!1, l2!1) AND --F(-l1!1)), -F(---F(-l1!1)))
    to -(-U(--F(-l1!1), -U(l1!1, l2!1) AND --F(-l1!1)) AND --F(---F(-l1!1)))
  W rewrites W(--F(-l1!1), -U(l1!1, l2!1) AND --F(-l1!1))
    to -(-U(--F(-l1!1), -U(l1!1, l2!1) AND --F(-l1!1)) AND --F(---F(-l1!1)))
  R rewrites R(-U(l1!1, l2!1), --F(-l1!1))
    to -(-U(--F(-l1!1), -U(l1!1, l2!1) AND --F(-l1!1)) AND --F(---F(-l1!1)))
  Trying repeated skolemization, instantiation, and if-lifting,
  this simplifies to: 
  Test01 :  
  
  {-1}  -(U(l1!1, l2!1)) = -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1))
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rerunning step: (use not_elim)
  Using lemma not_elim,
  this simplifies to: 
  Test01 :  
  
  {-1}  --F(--l2!1) = F(--l2!1)
  [-2]  -(U(l1!1, l2!1)) = -(-U(-l2!1, -l1!1 AND -l2!1) AND --F(--l2!1))
    |-------
  [1]   U(l1!1, l2!1) = (-(U(-l2!1, (-l1!1 AND -l2!1))) AND F(l2!1))
  
  Rerunning step: (grind)
  Trying repeated skolemization, instantiation, and if-lifting,
  Q.E.D.
  
  ```
  
  
  
  

### 存在问题

> 如何将LTL公式与bool结合   /  如何证明蕴含   

$$
\bullet F(\phi \wedge \psi) \Rightarrow F \phi \wedge F \psi
$$

$$
\bullet G \phi \vee G \psi \Rightarrow G(\phi \vee \psi)
$$

```
  F_or: axiom F(or_(l1,l2)) = or_(F(l1),F(l2))          //推导不出来   设为定理？
  
  F_and_false1: lemma impl(F(l1 and l2), (F(l1) and F(l2)))
  %%false Incompatible types for impl(F(l1 AND l2), (F(l1) AND F(l2)))
  %%Found: LTL Expected: booleans.booleanFound: LTL Expected: booleans.boolean
  
  F_and_false2: lemma F(l1 and l2) => (F(l1) and F(l2)) 
  %%false first argument to => has the wrong type
  %%Found: (future?)  Expected: booleans.bool
 
  G_and: lemma G(l1 and l2) = (G(l1) and G(l2))           //此时可以根据F_or 推导
  %%同理
  G_or: lemma  or_(G(l1),G(l2)) = G(or_(l1,l2)) 
```


$$
G(\phi \wedge \psi) \equiv G \phi \wedge G \psi
$$

```
G_and :  

  |-------
{1}   FORALL (l1, l2: LTL): G(l1 AND l2) = (G(l1) AND G(l2))

Rule? (skosimp)
Skolemizing and flattening,
this simplifies to: 
G_and :  

  |-------
{1}   G(l1!1 AND l2!1) = (G(l1!1) AND G(l2!1))

Rule? (grind)
G rewrites G(l1!1 AND l2!1)
  to -F(-(l1!1 AND l2!1))
G rewrites G(l1!1)
  to -F(-l1!1)
G rewrites G(l2!1)
  to -F(-l2!1)
Trying repeated skolemization, instantiation, and if-lifting,
this simplifies to: 
G_and :  

  |-------
{1}   -F(-(l1!1 AND l2!1)) = (-F(-l1!1) AND -F(-l2!1))

Rule? (use F_or)
Using lemma F_or,
this simplifies to: 
G_and :  

{-1}  F(or_(-(l1!1 AND l2!1), -(l1!1 AND l2!1))) =
       or_(F(-(l1!1 AND l2!1)), F(-(l1!1 AND l2!1)))
  |-------
[1]   -F(-(l1!1 AND l2!1)) = (-F(-l1!1) AND -F(-l2!1))

Rule? (grind)
or_ rewrites or_(-(l1!1 AND l2!1), -(l1!1 AND l2!1))
  to -(--(l1!1 AND l2!1) AND --(l1!1 AND l2!1))
or_ rewrites or_(F(-(l1!1 AND l2!1)), F(-(l1!1 AND l2!1)))
  to -(-F(-(l1!1 AND l2!1)) AND -F(-(l1!1 AND l2!1)))
Trying repeated skolemization, instantiation, and if-lifting,
Q.E.D.
```





+ LTL公式定义为bool类型    与命题逻辑保持一致
+ =  写成  <=>
+ 永真推理      p-> G(P)
+ 普通推理   

