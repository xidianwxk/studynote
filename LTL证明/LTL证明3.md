#### paper:PVS Embeddings of Propositional  Modal Logic



```
modalformula[pvars: TYPE+]: DATATYPE
BEGIN
    pvar(arg: pvars): var?
    ∼(arg: modalformula): not?
    &(arg1: modalformula, arg2: modalformula): and?
    =>(arg1: modalformula, arg2: modalformula): imp?
    □(arg: modalformula): box?
END modalformula
```

```
elem_deep_pml: THEORY
BEGIN
    worlds, pvars: TYPE+
    IMPORTING modalformula[pvars]
    
    w, v: VAR worlds
    x, y: VAR pvars
    P, Q: VAR modalformula
    
    val(x)(w): bool
    
    |=(w, P): RECURSIVE bool =
        CASES P OF
            pvar(x)  : val(x)(w),
            ∼(R)     : NOT (w |= R),
            &(R, S)  : (w |= R) AND (w |= S),
            =>(R, S) : (w |= R) IMPLIES (w |= S),【】
            □(R)	 : FORALL v: (v |= R)
        ENDCASES
    MEASURE P by <<
    
    等价于以下形式
    %% pmlformulas  : TYPE = [worlds -> bool]
    %% ∼(P)(w)		: bool = NOT P(w) ;
	%% &(P, Q)(w)	: bool = P(w) AND Q(w) ;
	%% =>(P, Q)(w)	: bool = P(w) IMPLIES Q(w) ;
	%% □(P)(w)		: bool = FORALL v: P(v) ;
	%% |=(w, P): bool = P(w)
    
    <>(P): modalformula = ∼ □ ∼ P ;
    
    valid(P): bool = FORALL w: w |= P
    
    validval(x: pvars): bool = valid(pvar(x))
    CONVERSION valid, pvar, validval
    
END elem_deep_pml
```

```
eg_elem_deep: THEORY
BEGIN IMPORTING elem_deep_pml

    g: pvars
    P: VAR modalformula
    
    H1: AXIOM <> g
    H2: AXIOM P => □ P
    HC: THEOREM g
    
END eg_elem_deep


```

```
H1 :  
  |-------
{1}   valid(F(pvar[pvars](g)))

H2 :  
  |-------
{1}   FORALL (P: modalformula[pvars]): valid(P => G(P))
```

+ 证明步骤

  ```
  HC :  
  
    |-------
  {1}   validval(g)
  
  Rerunning step: (lemma H1)
  Applying H1 
  this simplifies to: 
  HC :  
  {-1}  valid(F(pvar[pvars](g)))
    |-------
  [1]   validval(g)
  
  Rerunning step: (lemma H2)
  Applying H2 
  this simplifies to: 
  HC :  
  {-1}  FORALL (P: modalformula[pvars]): valid(P => G(P))
  [-2]  valid(F(pvar[pvars](g)))
    |-------
  [1]   validval(g)
  
  Rerunning step: (inst?)
  Found substitution:
  P: modalformula[pvars] gets pvar[pvars](g),
  Using template: P
  Instantiating quantified variables,
  this simplifies to: 
  HC :  
  {-1}  valid(pvar[pvars](g) => G(pvar[pvars](g)))
  [-2]  valid(F(pvar[pvars](g)))
    |-------
  [1]   validval(g)
  
  Rerunning step: (grind :polarity? t)
  |= rewrites (w |= pvar[pvars](g))
    to val(g)(w)
  |= rewrites (v |= pvar[pvars](g))
    to val(g)(v)
  |= rewrites (w |= G(pvar[pvars](g)))
    to FORALL v: val(g)(v)
  |= rewrites w |= pvar[pvars](g) => G(pvar[pvars](g))
    to val(g)(w) IMPLIES (FORALL v: val(g)(v))
  valid rewrites valid(pvar[pvars](g) => G(pvar[pvars](g)))
    to FORALL w: val(g)(w) IMPLIES (FORALL v: val(g)(v))
  F rewrites F(pvar[pvars](g))
    to fei(G(fei(pvar[pvars](g))))
  |= rewrites (v |= pvar[pvars](g))
    to val(g)(v)
  |= rewrites (v |= fei(pvar[pvars](g)))
    to NOT val(g)(v)
  |= rewrites (w |= G(fei(pvar[pvars](g))))
    to FORALL v: NOT val(g)(v)
  |= rewrites w |= fei(G(fei(pvar[pvars](g))))
    to NOT (FORALL v: NOT val(g)(v))
  valid rewrites valid(fei(G(fei(pvar[pvars](g)))))
    to FORALL w: NOT (FORALL v: NOT val(g)(v))
  |= rewrites w |= pvar(g)
    to val(g)(w)
  valid rewrites valid(pvar(g))
    to FORALL w: val(g)(w)
  validval rewrites validval(g)
    to FORALL w: val(g)(w)
  Trying repeated skolemization, instantiation, and if-lifting,
  Q.E.D.
  
  ```



##### **Timed automata for metric interval temporal logic formulae in prototype verifification system**

```
MITL: DATATYPE BEGIN
    TRUE: TURE?
    ∼(p : P): NOT?
    ∧(phi1, phi2: MITL): AND?
    ∨(phi1, phi2: MITL): OR?
    <>(I: B0I, psi: MITL): Ieventually?
    [](I: B0I, psi: MITL): Ialways?
    U(phi1: MITL, I1: BnzI, phi2: MITL): IU?
    W(phi1: MITL, I1: BnzI, phi2: MITL): W? 
    U_ (psi1,psi2: MITL): U? 
    G(psi: MITL): always?
END MITL


| = (tau: TimedStateSeq, phi: MITL): RECURSIVE bool = 
		CASES phi OF
            TRUE: TRUE,
            atom(p): st(tau)(0)(p),
            ∼(p): NOT st(tau)(0)(p),
            ∧(phi1, phi2): (tau | = phi1) AND (tau | = phi2),
            ∨(phi1, phi2): (tau | = phi1) OR (tau | = phi2),
            F (I, psi): ∃t : t ∈ I AND (suffix(tau)(t) |= psi),
            G (I, psi): ∀t : t ∈ I => (suffix(tau)(t) | = psi),
          ...
```



```
LTL_test4  % [ parameters ]
		: THEORY
  BEGIN

      % ASSUMING
      % assuming declarations
      % ENDASSUMING

      states: type+
      LTL: type=[states -> boolean]

      l,l1,l2: var LTL
      s,s1,s2: var states

      true: LTL= lambda s: true;
      false: LTL= lambda s: false;

      not: type = [LTL -> LTL]
      and: type = [LTL , LTL -> LTL]

      X: type = [LTL -> LTL]
      G: type = [LTL -> LTL]
      F: type = [LTL -> LTL]

      U: type = [LTL,LTL -> LTL]
      R: type = [LTL,LTL -> LTL]
      W: type = [LTL,LTL -> LTL]

      not(l)(s): bool = not l(s);
      and(l1,l2)(s): bool = l1(s) and l2(s);

      or(l1,l2): LTL = not(not(l1) and not(l2));
      impl(l1,l2): LTL = not(l1) or l2;

      G(l)(s): bool = forall s: l(s);        %%语义层次
      F(l): LTL = not(G(not l));


      %%U(l1,l2)(s): bool = ...       
      %%R(l1,l2): LTL = U(l1,l2) or G(l1);


      |=(s,l): bool = l(s)
      valid(l): bool = forall s: s|= l     %%是否可行？
         
  END LTL_test4
```

