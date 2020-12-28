

```shell
PPTL  % [ parameters ]
		: THEORY

  BEGIN

  % ASSUMING
   % assuming declarations
  % ENDASSUMING

 
%%%%%%%%type definition

    ITL : nonempty_type = boolean

    p,q,r         : var ITL
    l,l1,l2,l3    : var ITL

    p_plus	  : type = list[ITL];
    p_plu	  : var p_plus;
   
    U  		  : [ITL , ITL -> ITL];
    %skip	  : ITL;
    %chop	  : [ITL , ITL -> ITL];
    %chopstart	  : [ITL -> ITL];
    prj		  : [p_plus, ITL -> ITL];
    X		  : [ITL -> ITL];

    a, b, c: VAR set[ITL];

    pre1: lemma union(a, a) = a;
    pre:	    lemma (:l , l1  ,l2:) = (:l2 , l1, l:);
    testprj:		lemma prj((:l , l1  ,l2:), l3) <=> prj ((:l2 , l1, l:),l3);
    testprj1:		lemma prj((:l , l1  ,l2:), l3) <=> prj (cons(l , (:l1 , l2:)),l3);
    %first argument to prj has the wrong type
    %Found: [ITL, ITL, ITL]
    %Expected: p_plus
    %testprj2:		lemma prj((l , l1  ,l2), l3) <=> prj ((l2 , l1, l),l3)

    tt		  : ITL = true;
    ff		  : ITL = not tt;
    empty	  : ITL = not X(true);
    more	  : ITL	= not empty;
    
    chop(l1,l2)	  : ITL = prj((:l1 , l2:) , empty)
    F(l)	  : ITL = chop(tt , l)
    G(l)	  : ITL = not F(not l)
    chop-plus(l)	  : ITL = prj(p_plu , empty);
    chop-star(l)	  : ITL = empty or chop-plus(l); 
    halt(l)	  : ITL = G(empty <=> l)
    final(l)	  : ITL = G(empty => l)
    keep(l)	  : ITL = G(X(tt) => l)
    rem(l) 	  : ITL = G(X(tt) => X(l))
    
    inf		  : ITL = G(X(tt))
    fin		  : ITL = F(empty)
    parall(l1,l2) : ITL = l1 and (chop(l2,tt)) or l2 and (chop(l1,tt))

    R(l1,l2)	  : ITL = not (U(not l1 ,not l2))


    W_next(l)	  : ITL = not X(not l);
    some_init(l)  : ITL	= chop(l , true);
    all_init(l)	  : ITL = not some_init(not l);
   
%%%%%%%%%%%%%%Some logic laws of PPTL

    law1	  : lemma F(l) <=> l or X(F(l))
    law2	  : lemma G(l) <=> l and empty or l and X(G(l))
    law3	  : lemma p_plus <=> p or 

%%%%%%%%%%%%%%Inference rules
    Gamma	  : var ITL;
    Delta	  : var ITL;
    
    emptyconsequent : ITL = ff;
    emptyantecedent : ITL = tt;

    X			  : var ITL;
    *(Gamma , X)  : ITL = Gamma and X;
    +(Delta , X)  : ITL = Delta or X;
    
    SQT(Gamma , Delta) : bool = Gamma => Delta;     
    
    |-(Delta) : bool = SQT(emptyantecedent , Delta)
    
    MP		 :  axiom  |-(l1) and |-(l1 => l2)  => |-(l2);
    
   

    chop_left  : lemma  SQT(Gamma * chop(l1,l2) , Delta) and SQT(Gamma , Delta + G(l => l1))
    	      implies  SQT(Gamma * chop(l,l2) , Delta)


   


    %%% Future Axiom
    F0        :   axiom |-(G(l)  => l )
    F1	      :   axiom |-(G( X(not l) <=> not X(l) ))
    F2	      :   axiom |-(G( X(l1 => l2) => (X(l1) => X(l2)) ))
    F3	      :   axiom |-(G( G(l1 => l2) => (G(l1) => G(l2)) ))
    F4	      :   axiom |-(G(l) => G(X(l)))
    F5	      :   axiom |-(G(l => X(l))  =>  G(l => G(l)))
    F6	      :   axiom |-(G( U(l1,l2) <=> (l2 or l1 and X(U(l1,l2))) ))
    F7        :   axiom |-(G( U(l1,l2) => F(l2) ))

    ChopOrImpl	  	  :  axiom  |-(chop(l , l1 or l2) => chop(l,l1) or chop(l,l2) )
    BiBoxChopImpChop	  :  axiom  |-(all_init(l => l1) and G(l2 => l3) => chop(l,l2) => chop(l1,l3))

    INS			  :  axiom  l => |-(G(l))
    PAR			  :  axiom  |-(l) => l;
    BoxGen    		  :  axiom  |-(l) => |- (G(l))
    BiGen		  :  axiom  |-(l) => |- (all_init(l))


    BoxChopImpChop	  :  lemma  |-( G(l => l1) => (chop(l2,l) => chop(l2,l1)) )
    RightChopImplChop 	  :  lemma  |-( l=>l1 ) => |-(chop(l2,l) => chop(l2,l1))


    %A1	      :	  axiom |-( (p or p) => p )
    %A2	      :	  axiom |-( p => (p or q) )
    %A3	      :   axiom |-( (p or q) => (q or p) )
    %A4	      :	  axiom |-( (q => r) => ((p or q) => (p or r)) )
    
    A1	      :	  axiom |-( p => (q => p) )
    A2	      :	  axiom |-( (p => (q => r)) => ((p => q) => (p => r)) )
    A3	      :	  axiom |-( (not p => not q) => (q => p) )
   
    T1	      :	  lemma |-( p => p)
    T2	      :	  lemma |-( (q => r) => ((p => q) => (p => r)) )


    %%% 带入规则
    %%% Theorems and Rules of The Deductive System

    %%% particularization
    %PAR       :  lemma |-(G(l)) => |-(l)
   
    
    %%% entailment modus ponens
    %temp     :  lemma |-(l) => |-(G(l))

    E_MP_01  :	lemma |-(G(l1 => l))        and  |-(G(l1))    => |-(G(l))
    E_MP_02  :  lemma |-(G(l1 and l2 => l)) and  |-(G(l1))   and |-(G(l2)) => |-(G(l))


    F0_1      :	axiom |-(G(l)) => |-(l)
    PAR_01    :	lemma |-(G(l) => l)
    %%% entailment transitivity
    E_TRNS   :  lemma |-(G(l1 => l2)) and  |-(G(l2 => l))  =>  |-(G(l1 => l))
    R4	     :	lemma |-(G(l1 => l))  and  |-(G(l2 => l))  =>  |-(G(l1 or l2 => l))
    R5	     :	lemma |-(G(l => l1))  and  |-(G(l => l2))  =>  |-(G(l => l1 and l2))
    R6	     :	lemma |-(G(l1 => l2)) and  |-(G(l2 => l1)) =>  |-(G(l1 <=> l2))
    R1	     : 	lemma |-(G(l2))   	     =>   |-(G(l1 => l2))
    R2	     :	lemma |-(G(l1 => l2))        =>   |-(G(l1 => l2))  and |-(G(l2 => l1))
    R7	     :	lemma |-(G(l1 => (l2 => l))) =>   |-(G(l1 and l2 => l))

    %%% computational induction
    Rci	     :	lemma |-(G(l => X(l))) => |-(G(l => G(l)))
    %%% X generalization
    RXgen    :	lemma |-(G(l)) => |-(G(X(l)))
    %%% X is monotonic
    RXmon    :	lemma |-(G(l1 => l2)) => |-(G(X(l1) => X(l2)))


    Test_replace : lemma G(X(l1 and l2) <=> (X(l1) and X(l2)))  <=> F(l1)

    T3	     :  theorem	  |-(G (X(l1 and l2) <=> (X(l1) and X(l2)) ))
    T4	     :	theorem	  |-(G(G(l) => X(G(l))))   

    %%% G generalization
    RGgen    :	lemma	|-(G(l)) => |-(G(G(l)))
    %%% G is monotonic
    RGmon    :	lemma	|-(G(l1 => l2)) => |-(G(G(l1) => G(l2)))

    T5	     :	theorem	 |-(G(G(l1) or G(l2) => G(l1 or l2)))
    T6	     :	theorem	 |-(G(l1 and l2)) <=>  |-(G(l1) and G(l2))

  END PPTL
```

