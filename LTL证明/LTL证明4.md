### ITL_2

```
ITL_2  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING


  %%%% syntax:definition of datatype expression
  exp: DATATYPE
     BEGIN
       const(n: int)		: cst?		 : exp
       svariable(sv: nat) 	: svr?		 : exp
       variable(v: nat) 	: vr?		 : exp
       next_exp(next_exp : exp) : nx?	         : exp
       fin(finexp : exp)        : fin?	         : exp
       +(aex1: exp, aex2: exp)  : plus?	         : exp
       -(sex1: exp, sex2: exp)  : min?	         : exp
       *(mex1: exp, mex2: exp)  : mult?	         : exp
     END exp


  %%%% syntax:definition of datatype formula
  form : DATATYPE
      BEGIN
         importing exp
	 skip                            : skip?     : form
	 =(eqxp1: exp, eqxp2: exp)       : eqi?      : form
	 <(lexp1: exp, lexp2: exp)       : les?      : form
	 not(fn1: form)                  : inot?     : form
	 and(fa1: form, fa2: form)       : iand?     : form
	 chop(fc1: form, fc2: form)      : chop?     : form
	 chopstar(fcs1: form)            : chopstar? : form
     END form

  %%%% frequently used abbreviations
     
     f0,f1,f2,f3: var form
     %va,sva:var exp
     exp1,exp2: var exp
  
     T : form = (const(0)=const(0));            %true value
     F : form = not T;
     or(f1,f2) : form = not((not f1) and (not f2));
     =>(f1,f2) : form = (not f1) or f2;       %implies
     ==(f1,f2) : form = (f1 => f2) and (f2 => f1);     %equivalent
     X(f1) : form = chop(skip,f1);       %next f
     infinite : form =chop(T,F);           %infinite interval
     finite : form = not infinite;              %finite interval
     F(f1) : form = chop(finite,f1);    %F sometimes
     G(f1) : form = not(F(not f1));          %G always
     weak_X(f1) : form = not(X(not f1));         %weak_next
     Di(f1) : form = chop(f1,T);         %some initial subinterval
     Bi(f1) : form = not (Di(not f1));        %all initial subintervals
     asX(exp1,exp2) : form = next_exp(exp1) = exp2;
     more : form = X(T);
     empty : form = not more;
     final(f1) : form = G(empty => f1);       %final state
     while(f0, f1) : form = chopstar(f0 and f1) and final(not f0)   %while f0 do f1


   %%% Definition of some parameters
     Value: TYPE = int      		   %state variables are of the sort integers
     SValue: TYPE = int     		   %static variables are of the sort integers
     Vars: TYPE = nat      			   %Vars the indices of state variables(infinite number)
     SVars: TYPE = nat     			   %SVars the indices of static variables(infinite number)
     State: TYPE = [Vars -> Value]     %State(i): the ith state variable
     SState: TYPE = [SVars -> SValue]  %SState(i): the ith static variable


     importing sequence[State]
     Interval : TYPE = sequence[State]
     env : VAR SState         % env is a mapping form static variables to their values
     sigma : VAR Interval     % sigma is a sequence of mappings from state variables to their values

     IValue : TYPE = [SState,Interval -> Value]
     E1 : VAR IValue;
     x1: var Value
     
%%% lenght of an expression (needed for recursive definition)
     sizeexp(e:exp) : nat =
     reduce_nat(
     (LAMBDA (i : int): 1),                 %const(n) 
     (LAMBDA (i : nat): 1 + i ),     	    %svariable(sv)
     (LAMBDA (i : nat): 1 + i ),	    %variable(v)
     (LAMBDA (i : nat) : 1 + i), 	    %next_exp(exp1)
     (LAMBDA (i : nat) : 1 + i),            %fin(exp2)
     (LAMBDA (i, j : nat): 1 + i + j),	    %+(exp1, exp2)
     (LAMBDA (i, j : nat): 1 + i + j),	    %-(exp1, exp2)
     (LAMBDA (i, j : nat): 1 + i + j) 	    %*(exp1, exp2)
     )(e)


%%%% semantics of O(expression)             without epsilon
     semnext_exp(E1)(env,sigma) : Value =
     		 lambda x1 : if infinite(sigma) then
		 		   E1(env,suf(sigma,1))=x1
		             elsif len(sigma)>0 then
				   E1(env,sub(sigma,1,len(sigma)))=x1
			     else
				false
			     endif

%%%% semantics of fin(expression)
     semfin(E1)(env,sigma) : Value =
     			   epsilon(lambda x1 : if infinite(sigma) then
			   		  false else
			   E1(env,sub(sigma,len(sigma),len(sigma)))=x1
				endif)
				
     %% semantics of expression
     E(e : exp)(env,sigma) : RECURSIVE Valuse =
     	 CASES e OF
	 const(v)       : v,
	 variable(n)    : seq(sigma)(0)(n),
	 svariable(n)   : env(n),
	 next_exp(exp1) : semnext_exp(E(exp1))(env,sigma),
	 fin(exp1)      : semfin(E(exp1))(env,sigma),
	 +(exp1, exp2)  : E(exp1)(env,sigma) + E(exp2)(env,sigma),
	 -(exp1, exp2)  : E(exp1)(env,sigma) - E(exp2)(env,sigma),
	 *(exp1, exp2)  : E(exp1)(env,sigma) * E(exp2)(env,sigma)
	 ENDCASES
      MEASURE sizeexp(e)


    Iform : TYPE = [SState,Interval -> bool]
    F1,F2 : VAR Iform;

    %%%% lenght of an expression (needed for recursive definition)
    sizeform(f: form) : nat =
     reduce_nat(
     1,                                               %skip
     %(LAMBDA (i : nat): 1 + i ),                     %not(form1)
     (LAMBDA (i,j: exp): sizeexp(i)+sizeexp(j)),      %not
     (LAMBDA (i,j: exp): sizeexp(i)+sizeexp(j)),      %=(exp1,exp2)
     %(LAMBDA (i,j: exp): sizeexp(i)+sizeexp(j)),      %<(exp1,exp2)
     (LAMBDA (i :nat): i=1),	                      %<(exp1,exp2)
     (LAMBDA (i, j : nat): 1 + i + j),	              %chop(form1.form2)
     (LAMBDA (i, j : nat): 1 + i + j),	              %and(form1,form2)
     (LAMBDA (i : nat): 1 + i )    	              %chop*(form)
     )(f)


    %%%semantics of -f
     semnot(F1)(env,sigma) : bool = not F1(env,sigma)
    %%% semantics of f1 /\ f2
     semand(F1,F2)(env,sigma) : bool = F1(env,sigma) and F2(env,sigma)
    %%% semantics of f1ˆf2
     semchop(F1,F2)(env,sigma) : bool =
    	(EXISTS (m: nat):
	    ( (infinite(sigma) and F2(env,suf(sigma,m))) or
	          (not infinite(sigma) and m <= len(sigma) and
		       F2(env,sub(sigma, m, len(sigma)))
	           )
       	       )
	       and F1(env,sub(sigma, 0, m))
	    )
 	  or (infinite(sigma) and F1(env,sigma))


    %%% semantics of chopstar(f)
    %%%% importing theory of sequences instantiated for natural numbers
     importing sequence[nat]
     
    %%%% definition of infinite list of chopping points
     infinite_interval : TYPE = Infsequence[nat] 
    %%%% definiton of finite list of chopping points
     finite_interval : TYPE = Finsequence[nat]
      
     il: VAR infinite_interval
     fl: VAR finite_interval 

     %%%% semantics of chopstar(f)
     semchopstar(F1)(env,sigma) : bool =
      (IF infinite(sigma) THEN
         (EXISTS fl :
	       seq(fl)(0) = 0 and
	       F1(env,suf(sigma, seq(fl)(len(fl)))) and
	       (FORALL (i: below[len(fl)]):
	           seq(fl)(i) < seq(fl)(i + 1) and
		   F1(env,sub(sigma, seq(fl)(i), seq(fl)(i + 1)))
	       )
	)
OR
	(EXISTS il:
	   seq(il)(0) = 0 and
	   (FORALL (i: nat):
	       seq(il)(i) < seq(il)(i + 1) and
	       F1(env,sub(sigma, seq(il)(i), seq(il)(i + 1)))
	   )
        )
     ELSE
        (EXISTS fl :
	   seq(fl)(0) = 0 and
	   seq(fl)(len(fl)) = len(sigma) and
	   (FORALL (i: below[len(fl)]):
	       seq(fl)(i+1) <= len(sigma) and
	       seq(fl)(i) < seq(fl)(i + 1) and
	       F1(env,sub(sigma, seq(fl)(i), seq(fl)(i + 1)))
           )
        )
     ENDIF)
     
    %%%% semantics of formulae
    M(f:form)(env,sigma) : RECURSIVE bool =
    	CASES f OF	
	 skip         : (len(sigma) = 1 and not infinite(sigma)),
	 not(f1)      : semnot(M(f1))(env,sigma),
	 =(exp1,exp2) : E(exp1)(env,sigma) = E(exp2)(env,sigma),
	 <(exp1,exp2) : E(exp1)(env,sigma) < E(exp2)(env,sigma),
	 chop(f1,f2)  : semchop(M(f1),M(f2))(env,sigma),
	 and(f1,f2)   : semand(M(f1),M(f2))(env,sigma),
	 chopstar(f1) : semchopstar(M(f1))(env,sigma)
	 ENDCASES
       MEASURE sizeform(f)



%%%*********************************************************************
    %%%% definition of validity of formulae
     V:pred[form]= 
     	(LAMBDA f1: (FORALL (env, sigma): M(f1)(env,sigma))) % CONVERSION V

    %%%% the axioms
     ChopOrImp: LEMMA V((chop(f0,(f1 or f2))) => (chop(f0,f1) or chop(f0,f2)))
     BiBoxChopImpChop: LEMMA
     		V((Bi(f0 => f1) and G(f2 => f3)) => (chop(f0,f2) =>chop(f1,f3)))
    %%%% the rules
     MP: LEMMA V(f0 => f1) AND V(f0) IMPLIES V(f1) 
     BoxGen: LEMMA V(f0) IMPLIES V(G(f0))
     BiGen: LEMMA V(f0) IMPLIES V(Bi(f0))

    % V_OR: LEMMA V(f0 or f1) IMPLIES V(f0) or V(f1)        %%not really true
    % V_OR_CON: LEMMA V(f0) or V(f1) IMPLIES V(f0 or f1)
    % V_AND: LEMMA V(f0 and f1) IMPLIES V(f0) and V(f1)
    % V_AND_CON: LEMMA   V(f0) and V(f1) IMPLIES V(f0 and f1)
    % V_Imp: LEMMA V(f0) implies FORALL (env, sigma): M(f0)(env,sigma)

    Test: LEMMA V(f2 => f2) implies true
    Test_1: Lemma true implies V(f0 => f0)
    Test_2: Lemma true implies V(f0 => f0 or f2)
    Test_3: Lemma true implies V(f0 => f2 or f0)


     BoxChopImpChop: LEMMA V(G(f0 =>f1)) implies V(chop(f2,f0)=>chop(f2,f1))

     RightChopImpChop: LEMMA V(f0 => f1) implies V(chop(f2,f0) => chop(f2,f1))

     ChopOrEqv: LEMMA V(chop(f0,(f1 or f2))) <=> V(chop(f0,f1) or chop(f0,f2))

     p,q,r: bool % Propositional constants   

     prop_0: LEMMA ((p => q) AND p) => q 
     prop_1: LEMMA NOT (p OR q) IFF (NOT p AND NOT q)
     prop_2: LEMMA ((p => q) => (p AND q)) IFF ((NOT p => q) AND (q => p))

     
     
  END ITL_2

```



### LTL_test2

```
LTL_test2  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

 % Prop: datatype
 %   begin

 %   not(p:bool): not?
 %   and(p: bool ,q:bool): and?
 %   end Prop



  P: type = boolean
  
  LTL: datatype
    begin
    true: true?
    atom(p:P): atom?
    not(l1:LTL): not?
    and(l1,l2: LTL): and?

    X(l1:LTL): next?
    F(l1: LTL): future?
    U(l1,l2:LTL): until?
    end LTL


  l1,l2: var LTL
  false: LTL = not true
  or_(l1,l2) : LTL = not(not (l1) and not (l2))
  impl(l1,l2) : LTL = or_(not (l1),l2)
  G(l1): LTL = not F(not (l1))
  W(l1,l2): LTL = or_ (U(l1,l2),G(l1))
  R(l1,l2): LTL = W(l2,l1 and l2)

  
  not_X: axiom not X(l1) = X(not (l1))
  not_and: axiom not (l1 and l2) = and(not (l1),not (l2))
  not_elim: axiom not (not (l1)) = l1
  
  not_or: lemma (not(or_(l1,l2))) = or_(not l1 , not l2)
  F2G: lemma (not F(l1)) = G(not (l1))
  G2F: lemma (not G(l1)) = F(not (l1))
  U2R: lemma (not (U(l1,l2))) =  R(not (l1),not (l2))
  

END LTL_test2

```



###LTL_test3

```
LTL_test3  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING


  P: type = boolean
  
  LTL: datatype
    begin
        true			: true?
        atom(p:P)		: atom?
        -(l1:LTL)		: not?
        and(l1,l2: LTL)		: and?

        X(l1:LTL)  		: next?
        F(l1: LTL)		: future?
        U(l1,l2:LTL)		: until?
    end LTL


  l1,l2      : var LTL 
  false      : LTL = -true
  or_(l1,l2) : LTL = -(-l1 and -l2)
  impl(l1,l2): LTL = or_(-l1,l2)
  G(l1)      : LTL = -F(-l1)
  W(l1,l2)   : LTL = or_ (U(l1,l2),G(l1))
  R(l1,l2)   : LTL = W(l2,l1 and l2)

  
  not_X       : axiom - X(l1) = X(-l1)
  not_and     : axiom -(l1 and l2) = or_(-l1,-l2)
  not_elim    : axiom --l1 = l1
  G_X_swap    : axiom X(G(l1)) = G(X(l1))
  F_equal_T_U : axiom F(l1) = U(true,l1)
  
  
  not_or      : lemma -(or_(l1,l2)) = and(-l1,-l2)
  F2G	      : lemma -F(l1) = G(-l1)
  G2F	      : lemma -G(l1) = F(-l1)
  U2R	      : lemma -(U(l1,l2)) = R(-l1,-l2)


  F_or	      : lemma F(or_(l1,l2)) = or_(F(l1),F(l2))
  
  %F_and      : lemma impl(F(l1 and l2), (F(l1) and F(l2)))       %%%%false   Found: LTL Expected: booleans.boolean
  %F_and_1    : lemma F(l1 and l2)=> (F(l1) and F(l2))       %%implies
  
  G_and	      : lemma G(l1 and l2) = (G(l1) and G(l2))
  %G_or	      : lemma  or_(G(l1),G(l2)) = G(or_(l1,l2))    %%implies

  

  %F_equal_T_U : axiom F(l1) = U(true,l1)
  G_equal_F_R : lemma G(l1) = R(false,l1)
  U2W_F	      : lemma U(l1,l2) = and((W(l1,l2)), F(l2))
  %U2W_F      : lemma U(l1,l2) = (W(l1,l2)) and F(l2)
  %first argument to AND has the wrong type Found: booleans.boolean Expected: LTL
  W2U_G	      : lemma W(l1,l2) = or_(U(l1,l2),G(l1))

  W2R	      : lemma W(l1,l2) = R(l2,or_(l1,l2))
  R2W	      : lemma R(l1,l2) = W(l2,and(l1,l2))


  Fnot_notG   : lemma F(-l1) = -G(l1)
  G_not_notF  : lemma G(-l1) = -F(l1)
 
  U_double    : lemma U(l1,l2) = U(l1,U(l1,l2))

  Test01      : lemma U(l1,l2) = and(-(U(-l2,and(-l1,-l2))),F(l2))
  
END LTL_test3
```

###LTL_test4

```
LTL_test4  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

  states: type+
  
  LTL: type=[states -> boolean]
  R: [states -> states]

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

  G(l)(s): bool = forall s: l(s);
  F(l): LTL = not(G(not l));


  %X(l)(s): bool = lambda s: exists s1: R(s)=s1 and l(s1) 
  %not_X : axiom not X(l) = X(not l);

  
  
  U(l1,l2)(s): bool = lambda s: exists s1:(l2(s1) and R(s)=s1 and (
  	       forall s2:((R(s)=s2 and R(s2)=s1 and s2/=s1) implies l1(s2))));
  %%R(l1,l2): LTL = U(l1,l2) or G(l1);
  |=(s,l): bool = l(s)
  valid(l): bool = forall s: s|= l

  G_F: lemma (impl(G(l), F(l)))(s)
      
     
  END LTL_test4

```



###LTL_test5

```
LTL_test5  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

  
  LTL: type
  %true: var bool
  false: var bool

  not:  type = [LTL -> bool]
  and:  type = [LTL , LTL -> bool]
  %and: type = [bool , bool -> bool]
  or :  type = [LTL , LTL -> bool]
  impl :  type = [LTL , LTL -> bool] 

 
  
  X: type = [LTL -> bool]
  F: type = [LTL -> bool]
  U: type = [LTL , LTL -> bool]
  W: type = [LTL , LTL -> bool]
  R: type = [LTL , LTL -> bool]

  l1,l2      : var LTL 
  %false      : LTL = not true
  or(l1, l2) : bool  = not( not F(l1) and not F(l2))
  impl(l1,l2) : LTL = or(not l1,l2)
  G(l1)      : LTL = not F(not l1)
  W(l1,l2)   : LTL  = or(U(l1,l2),G(l1))
  R(l1,l2)   : LTL  = W(l2,l1 and l2)
 
  %%some equals
  %%false = not true




  p: var bool
  true_1: lemma true implies true
  p_true: lemma p or not p implies true

  END LTL_test5

```



### LTL_test6

```
LTL_test6  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

  
    state: type+
    forms: type = [state -> bool]

    
    s,s1,s2: var state
    f,f1,f2: var forms

    fei: type = [forms -> forms];
    he: type = [forms , forms -> forms];
    huo:  type = [forms , forms -> forms];
    impl:  type = [forms , forms -> forms];

    X: type = [forms -> forms]
    F: type = [forms -> forms]
    G: type = [forms -> forms]
    U: type = [forms , forms -> forms]
    W: type = [forms , forms -> forms]
    R: type = [forms , forms -> forms]

    true: bool
    false: bool

    huo(f1,f2) = fei(fei(f1) and fei(f2))
  END LTL_test6

```

```
LTL_Test6  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

    states: type+
    forms: type = [states -> bool]
    
    s,s1,s2: var states
    f,f1,f2: var forms


   % ~: type = [forms -> forms];

    ~(f)(s): bool = not f(s);
    and(f1,f2)(s): bool = f1(s) and f2(s); 
    =>(f1,f2)(s): bool = f1(s) implies f2(s);
    G(f)(s): bool =  forall s: f(s);

    F(f): forms = ~G(~f);
    

    |=(s,f): bool = f(s)
    valid(f): bool = forall s: s|= f

    H1: axiom valid(f => G(f))
    

  END LTL_Test6
```

###LTL_test7

```
LTL_test7 [T:type] % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING


    p,q,r: var bool
    l: var bool
    l1: var bool
    l2: var bool

    test1: lemma l1 and l2 implies l1
    test2: lemma l1 and l2 <=> l2 and l1
    test3: lemma  l1 and not l1 implies true

    not1: type = [bool -> bool]
    and1: type = [bool , bool -> bool]
    
    not1(l1) : bool = not(l1)
    test4    :lemma  not1(not1(l)) <=> l


    and1(l1,l2) : bool = and(l1,l2)
    or1(l1,l2)  : bool = not1(and1(not1(l1), not1(l2)))
    impl(l1,l2) : bool = or1(not1(l1),l2)

    test5: lemma l implies or1(l,l2)
    
    X: type = [bool -> bool]
    F: type = [bool -> bool]
    G: type = [bool -> bool]
    U: type = [bool , bool -> bool]
    W: type = [bool , bool -> bool]
    R: type = [bool , bool -> bool]
    
    F: [bool -> bool]   
    X: [bool -> bool]
    U: [bool,bool -> bool]

    %%%未申明F: [bool -> bool]   报错：No resolution for F with arguments of possible types: NOT l1 : booleans.bool
    
    G(l1) : bool =not1(F(not1 (l1))
    W(l1,l2): bool = or1(U(l1,l2),G(l1))
    R(l1,l2): bool = W(l2,and1(l1,l2))


	%%%证明时，如果需要用到axiom4的公理，需要在它之后定义
    test6: lemma not1(X(l)) <=> X(not1(l))
    
    axiom1: axiom not1(F(l)) <=> G(not1(l))
    axiom2: axiom G(impl(l1,l2)) => (impl(G(l1),G(l2)))
    axiom3: axiom G(l) => l
    axiom4: axiom X(not1(l)) <=> not(X(l))
    axiom5: axiom X(impl(l1, l2)) => (impl(X(l1), X(l2)))
    axiom6: axiom G(l) => F(l)
    axiom7: axiom G(l) => X(G(l))
    axiom7_1: axiom impl(G(l),X(G(l)))
    axiom8: axiom G(impl(l,X(l))) => (impl(l,G(l)))
    axiom9: axiom U(l1,l2) <=> (or1(l2,and1(l1,X(U(l1,l2)))))


    F_equal_T_U	: axiom F(l) <=> U(true,l)
    G_X_swap	: axiom X(G(l)) <=> G(X(l))


    and2or		: lemma not l1 and not l2 <=> not (l1 or l2)

    U2R			: lemma not1(U(l1,l2)) <=> R(not1(l1),not1(l2))
    F2G			: lemma not1(F(l)) <=> G(not1(l))
    F_or		: lemma F(or1(l1,l2)) <=> or1(F(l1),F(l2))
    F_and		: lemma F(and1(l1,l2)) => and1(F(l1),F(l2))
    G_and	    : lemma G(l1 and l2) <=> (G(l1) and G(l2))
    G_or		: lemma or1(G(l1),G(l2)) => G(or1(l1,l2))


    G_equal_F_R : lemma G(l) <=> R(false,l)
    U2W_F 		: lemma U(l1,l2) <=> and1((W(l1,l2)), F(l2))
    W2U_G 		: lemma W(l1,l2) <=> or1(U(l1,l2),G(l1))

    W2R			: lemma W(l1,l2) <=> R(l2,or1(l1,l2))
    R2W 		: lemma R(l1,l2) <=> W(l2,and1(l1,l2))

    

   Fnot_notG   : lemma F(not l1) = not G(l1)
   G_not_notF  : lemma G(not l1) = not F(l1)
 
 
   U_double    : lemma U(l1,l2) = U(l1,U(l1,l2))
   Test01      : lemma U(l1,l2) = and(not (U(not l2,and(not l1,not l2))),F(l2))
    

   V(l): bool = (l => true)  
   MP: lemma V(l1 => l2) and V(l1) => V(l2)

   F2T   : lemma false <=> not1 (true)
   F2T_1 : lemma false implies true
   
  END LTL_test7

```



###LTL_test8

```
LTL_test8  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

    LTL: nonempty_type = boolean

    p,q,r      : var bool
    l,l1,l2    : var LTL

    test1      : lemma l1 and l2 implies l1
    test2      : lemma l1 and l2 <=> l2 and l1
    test3      : lemma  l1 and not l1 implies true


    X: type = [LTL -> LTL]
    F: type = [LTL -> LTL]
    G: type = [LTL -> LTL]
    U: type = [LTL , LTL -> LTL]
    W: type = [LTL , LTL -> LTL]
    R: type = [LTL , LTL -> LTL]

    X: [LTL -> LTL]
    U: [LTL , LTL -> LTL]

    not		: type = [LTL -> LTL]
    not(l)	: LTL = not l
    test4	: lemma not not l <=> l

    F(l)	: LTL = U(true,l)
    R(l1,l2): LTL = not (U(not l1 , not l2))
    G(l)	: LTL = R(false , l)
    W(l1,l2): LTL = U(l1,l2) or G(l1)

    U2F		: lemma U(true,l) <=> F(l)
    U2G		: lemma U(true , not l) <=> not G(l)

    X_not	: axiom not X(l) <=> X(not l)
    G_X_swap: axiom X(G(l)) <=> G(X(l))
    
 
    F2G		: lemma F(not l) <=> not G(l)
    F_or	: axiom F(l1 or l2) <=> F(l1) or F(l2)
    F_and	: axiom F(l1 and l2) => F(l1) and F(l2)
    G_and	: axiom G(l1 and l2) <=> G(l1) and G(l2)
    G_or 	: axiom G(l1) or G(l2) => G(l1 or l2)

    and_or	: lemma not l1 and not l2 <=> not (l1 or l2)
  
  
    U2R	    : lemma not U(l1,l2) <=> R(not l1,not l2)

    W2R		: lemma W(l1,l2) <=> R(l2,l1 or l2)
    R2W 	: lemma R(l1,l2) <=> W(l2,l1 and l2)
  
    W2U_G   : lemma W(l1,l2) <=> U(l1,l2) or G(l1)
    U2W_F   : lemma U(l1,l2) <=> W(l1,l2) and F(l2)


    Fnot_notG    : lemma F(not l1) <=> not G(l1)
    G_not_notF   : lemma G(not l1) <=> not F(l1)

    G_equal_F_R  : lemma G(l) <=> R(false,l)
    F_equal_T_U  : lemma F(l) <=> U(true,l)


    U_double    : lemma U(l1,l2) <=> U(l1,U(l1,l2))
    %Test01      : lemma U(l1,l2) <=> and(not (U(not l2,and(not l1,not l2))),F(l2))
    Test01      : lemma U(l1,l2) <=> not(U(not l2,not l1 and not l2)) and F(l2)
    
```



>```
>U_double :  
>
>  |-------
>{1}   FORALL (l1, l2: LTL): U(l1, l2) <=> U(l1, U(l1, l2))
>
>Rule? (skosimp)
>Skolemizing and flattening,
>this simplifies to: 
>U_double :  
>
>  |-------
>{1}   U(l1!1, l2!1) <=> U(l1!1, U(l1!1, l2!1))
>
>Rule? (lemma U2R)
>Applying U2R 
>this simplifies to: 
>U_double :  
>
>{-1}  FORALL (l1, l2: LTL): NOT U(l1, l2) <=> R(NOT l1, NOT l2)
>  |-------
>[1]   U(l1!1, l2!1) <=> U(l1!1, U(l1!1, l2!1))
>
>Rule? (lemma R2W)
>Applying R2W 
>this simplifies to: 
>U_double :  
>
>{-1}  FORALL (l1, l2: LTL): R(l1, l2) <=> W(l2, l1 AND l2)
>[-2]  FORALL (l1, l2: LTL): NOT U(l1, l2) <=> R(NOT l1, NOT l2)
>  |-------
>[1]   U(l1!1, l2!1) <=> U(l1!1, U(l1!1, l2!1))
>
>Rule? (lemma W2U_G)
>Applying W2U_G 
>this simplifies to: 
>U_double :  
>
>{-1}  FORALL (l1, l2: LTL): W(l1, l2) <=> U(l1, l2) OR G(l1)
>[-2]  FORALL (l1, l2: LTL): R(l1, l2) <=> W(l2, l1 AND l2)
>[-3]  FORALL (l1, l2: LTL): NOT U(l1, l2) <=> R(NOT l1, NOT l2)
>  |-------
>[1]   U(l1!1, l2!1) <=> U(l1!1, U(l1!1, l2!1))
>
>Rule? (grind)
>NOT rewrites NOT FALSE
>  to TRUE
>NOT rewrites NOT l1
>  to NOT l1
>NOT rewrites NOT (U(TRUE, NOT l1))
>  to NOT (U(TRUE, NOT l1))
>.....
>.....
>R rewrites R(TRUE, TRUE)
>  to FALSE
>NOT rewrites NOT TRUE
>  to FALSE
>R rewrites R(TRUE, TRUE)
>  to TRUE
>Trying repeated skolemization, instantiation, and if-lifting,
>this yields  3 subgoals: 
>U_double.1 :  
>{-1}  W(l1!1, FALSE)
>{-2}  (U(FALSE, FALSE))
>{-3}  (U(TRUE, FALSE))
>{-4}  U(l1!1, FALSE)
>  |-------
>{1}   U(TRUE, TRUE)
>{2}   U(l1!1, TRUE)
>
>Rule? (postpone)
>Postponing U_double.1.
>U_double.2 :  
>
>{-1}  (U(FALSE, FALSE))
>{-2}  (U(TRUE, FALSE))
>{-3}  U(l1!1, FALSE)
>  |-------
>{1}   U(TRUE, TRUE)
>{2}   U(l1!1, l2!1)
>
>Rule? (postpone)
>Postponing U_double.2.
>U_double.3 :  
>
>{-1}  U(TRUE, TRUE)
>{-2}  U(l1!1, FALSE)
>  |-------
>{1}   (U(FALSE, FALSE))
>{2}   U(l1!1, l2!1)
>
>Rule? 
>```



>```
>Test01 :  
>
>  |-------
>{1}   FORALL (l1, l2: LTL):
>        U(l1, l2) <=> NOT (U(NOT l2, NOT l1 AND NOT l2)) AND F(l2)
>
>Rerunning step: (skosimp)
>Skolemizing and flattening,
>this simplifies to: 
>Test01 :  
>
>  |-------
>{1}   U(l1!1, l2!1) <=> NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1)) AND F(l2!1)
>
>Rerunning step: (lemma U2R)
>Applying U2R 
>this simplifies to: 
>Test01 :  
>
>{-1}  FORALL (l1, l2: LTL): NOT U(l1, l2) <=> R(NOT l1, NOT l2)
>  |-------
>[1]   U(l1!1, l2!1) <=> NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1)) AND F(l2!1)
>
>Rerunning step: (inst -1 l1!1 l2!1)
>Instantiating the top quantifier in -1 with the terms: 
> l1!1, l2!1,
>this simplifies to: 
>Test01 :  
>
>{-1}  NOT U(l1!1, l2!1) <=> R(NOT l1!1, NOT l2!1)
>  |-------
>[1]   U(l1!1, l2!1) <=> NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1)) AND F(l2!1)
>
>Rerunning step: (lemma R2W)
>Applying R2W 
>this simplifies to: 
>Test01 :  
>
>{-1}  FORALL (l1, l2: LTL): R(l1, l2) <=> W(l2, l1 AND l2)
>[-2]  NOT U(l1!1, l2!1) <=> R(NOT l1!1, NOT l2!1)
>  |-------
>[1]   U(l1!1, l2!1) <=> NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1)) AND F(l2!1)
>
>Rerunning step: (inst -1 "not l1!1" "not l2!1")
>Instantiating the top quantifier in -1 with the terms: 
> not l1!1, not l2!1,
>this simplifies to: 
>Test01 :  
>
>{-1}  R(NOT l1!1, NOT l2!1) <=> W(NOT l2!1, NOT l1!1 AND NOT l2!1)
>[-2]  NOT U(l1!1, l2!1) <=> R(NOT l1!1, NOT l2!1)
>  |-------
>[1]   U(l1!1, l2!1) <=> NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1)) AND F(l2!1)
>
>Rerunning step: (lemma W2U_G)
>Applying W2U_G 
>this simplifies to: 
>Test01 :  
>
>{-1}  FORALL (l1, l2: LTL): W(l1, l2) <=> U(l1, l2) OR G(l1)
>[-2]  R(NOT l1!1, NOT l2!1) <=> W(NOT l2!1, NOT l1!1 AND NOT l2!1)
>[-3]  NOT U(l1!1, l2!1) <=> R(NOT l1!1, NOT l2!1)
>  |-------
>[1]   U(l1!1, l2!1) <=> NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1)) AND F(l2!1)
>
>Rerunning step: (inst -1 "not l2!1" "not l1!1 AND not l2!1")
>Instantiating the top quantifier in -1 with the terms: 
> not l2!1, not l1!1 AND not l2!1,
>this simplifies to: 
>Test01 :  
>
>{-1}  W(NOT l2!1, NOT l1!1 AND NOT l2!1) <=>
>       U(NOT l2!1, NOT l1!1 AND NOT l2!1) OR G(NOT l2!1)
>[-2]  R(NOT l1!1, NOT l2!1) <=> W(NOT l2!1, NOT l1!1 AND NOT l2!1)
>[-3]  NOT U(l1!1, l2!1) <=> R(NOT l1!1, NOT l2!1)
>  |-------
>[1]   U(l1!1, l2!1) <=> NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1)) AND F(l2!1)
>
>Rerunning step: (skosimp)
>Skolemizing and flattening,
>this simplifies to: 
>Test01 :  
>
>{-1}  W(NOT l2!1, NOT l1!1 AND NOT l2!1) IMPLIES
>       U(NOT l2!1, NOT l1!1 AND NOT l2!1) OR G(NOT l2!1)
>{-2}  U(NOT l2!1, NOT l1!1 AND NOT l2!1) OR G(NOT l2!1) IMPLIES
>       W(NOT l2!1, NOT l1!1 AND NOT l2!1)
>{-3}  R(NOT l1!1, NOT l2!1) IMPLIES W(NOT l2!1, NOT l1!1 AND NOT l2!1)
>{-4}  W(NOT l2!1, NOT l1!1 AND NOT l2!1) IMPLIES R(NOT l1!1, NOT l2!1)
>{-5}  NOT U(l1!1, l2!1) IMPLIES R(NOT l1!1, NOT l2!1)
>{-6}  R(NOT l1!1, NOT l2!1) IMPLIES NOT U(l1!1, l2!1)
>  |-------
>[1]   U(l1!1, l2!1) <=> NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1)) AND F(l2!1)
>
>Rerunning step: (grind)
>NOT rewrites NOT l1!1
>  to NOT l1!1
>NOT rewrites NOT l2!1
>  to NOT l2!1
>NOT rewrites NOT NOT l1!1
>  to l1!1
>....
>....
>NOT rewrites NOT (U(l1!1, l2!1))
>  to NOT (U(l1!1, l2!1))
>R rewrites R(NOT l1!1, NOT l2!1)
>  to NOT (U(l1!1, l2!1))
>NOT rewrites NOT TRUE
>  to FALSE
>NOT rewrites NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1))
>  to NOT (U(NOT l2!1, NOT l1!1 AND NOT l2!1))
>Trying repeated skolemization, instantiation, and if-lifting,
>Q.E.D.
>```

![image-20201218171624178](LTL证明4.assets\image-20201218171624178.png)

```

    axiom1:   axiom     not F(l) <=> G(not l)
    axiom2:   axiom     G(l1 implies l2) => G(l1) implies G(l2)
    axiom3:   axiom	    G(l) => l
    axiom4:   axiom 	X(not l) <=> not(X(l))
    axiom5:   axiom 	X(l1 implies l2) => X(l1) implies X(l2)
    axiom6:   axiom 	G(l) => F(l)
    axiom7:   axiom 	G(l) => X(G(l))
    axiom8:   axiom 	G(l implies X(l)) => l implies G(l)
    axiom9:   axiom 	U(l1,l2) <=> (l2 or l1 and X(U(l1,l2)))
    axiom10:  axiom	    U(l1,l2) => F(l2)

    V(l)	: bool = (l => true)    %CONVERSION V

    chongyan  : lemma l implies true => V(l)
    MP		  : lemma V(l1 => l2) and V(l1) => V(l2)
    yinru	  : lemma V(l) implies V(G(l))

  END LTL_test8
```

### LTL_test9

```
LTL_test9  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

    LTL: nonempty_type = boolean

    p,q,r      : var bool
    l,l1,l2    : var LTL

    test1      : lemma l1 and l2 implies l1
    test2      : lemma l1 and l2 <=> l2 and l1
    test3      : lemma  l1 and not l1 implies true


    X: type = [LTL -> LTL]
    F: type = [LTL -> LTL]
    G: type = [LTL -> LTL]
    U: type = [LTL , LTL -> LTL]
    W: type = [LTL , LTL -> LTL]
    R: type = [LTL , LTL -> LTL]

    X: [LTL -> LTL]
    U: [LTL , LTL -> LTL]

    not: type = [LTL -> LTL]
     
    test4: lemma not not l <=> l

    F(l): LTL = U(true,l)
    R(l1,l2): LTL = not (U(not l1 , not l2))
    G(l): LTL = R(false , l)
    W(l1,l2): LTL = U(l1,l2) or G(l1)

    U2F: lemma U(true,l) <=> F(l)
    U2G: lemma U(true , not l) <=> not G(l)

    X_not: axiom not X(l) <=> X(not l)
    G_X_swap: axiom X(G(l)) <=> G(X(l))
    
 
    F2G	 : lemma F(not l) <=> not G(l)
    F_or : axiom F(l1 or l2) <=> F(l1) or F(l2)
    F_and: axiom F(l1 and l2) => F(l1) and F(l2)
    G_and: axiom G(l1 and l2) <=> G(l1) and G(l2)
    G_or : axiom G(l1) or G(l2) => G(l1 or l2)

    and_or: lemma not l1 and not l2 <=> not (l1 or l2)

    U2R : lemma not U(l1,l2) <=> R(not l1,not l2)
    

    W2R	  : lemma W(l1,l2) <=> R(l2,l1 or l2)
    R2W   : lemma R(l1,l2) <=> W(l2,l1 and l2)
    
    W2U_G : lemma W(l1,l2) <=> U(l1,l2) or G(l1)
    U2W_F : lemma U(l1,l2) <=> W(l1,l2) and F(l2)

    Fnot_notG   : lemma F(not l1) <=> not G(l1)
    G_not_notF  : lemma G(not l1) <=> not F(l1)

    G_equal_F_R: lemma G(l) <=> 
    
    
    R(false,l)
    F_equal_T_U: lemma F(l) <=> U(true,l)



    U_double    : lemma U(l1,l2) <=> U(l1,U(l1,l2))
    Test01      : lemma U(l1,l2) <=> not(U(not l2,not l1 and not l2)) and F(l2)

    axiom1:   axiom not F(l) <=> G(not l)
    axiom2:   axiom G(l1 implies l2) => G(l1) implies G(l2)
    axiom3:   axiom G(l) => l
    axiom4:   axiom X(not l) <=> not(X(l))
    axiom5:   axiom X(l1 implies l2) => X(l1) implies X(l2)
    axiom6:   axiom G(l) => F(l)
    axiom7:   axiom G(l) => X(G(l))
    axiom8:   axiom G(l implies X(l)) => l implies G(l)
    axiom9:   axiom U(l1,l2) <=> (l2 or l1 and X(U(l1,l2)))

    U_T_T: lemma  (true) => (U(true , true))
    U_L_T: lemma true => U(l,true)

    U_double_1 : lemma U(l1,l2) <=> U(l1,U(l1,l2))
    axiom10:  axiom U(l1,l2) => F(l2)

  
    V:pred[LTL] = lambda l: (true => l) 

    
    valid(l) : bool = true implies l; 
   % validval(ll: LTL) : bool = valid(ll) conversion valid , validval
    axiom12:   lemma V( not F(l)) <=>  V(G(not l))
    test: lemma V(l and not l)

    testt: lemma F(true)
    testtt: lemma G(true)     %xxxxx
    testttt: lemma not G(false)

    chongyan: lemma (true => l) => V(l)
    MP: lemma V(l1 => l2) and V(l1) => V(l2)
    yinru: lemma V(l) implies V(G(l)) 

   

  END LTL_test9
```



