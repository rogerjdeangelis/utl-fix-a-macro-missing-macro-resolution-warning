# utl-fix-a-macro-missing-macro-resolution-warning
    SAS Forum: SAS bug no warning for unresolvable macro variable?                                                                    
                                                                                                                                      
    github                                                                                                                            
    https://tinyurl.com/y2kymd38                                                                                                      
    https://github.com/rogerjdeangelis/utl-sas-bug-no-warning-for-unresolvable-macro-variable                                         
                                                                                                                                      
    SAS Forum                                                                                                                         
    https://tinyurl.com/y4h6jn6q                                                                                                      
    https://communities.sas.com/t5/SASware-Ballot-Ideas/Throw-warning-every-time-macro-variable-reference-cannot-be/idi-p/585905      
                                                                                                                                      
                                                                                                                                      
    Q Profile                                                                                                                         
    Quentin McMullen                                                                                                                  
    qmcmullen.sas@gmail.com                                                                                                           
    https://communities.sas.com/t5/user/viewprofilepage/user-id/19879                                                                 
                                                                                                                                      
    I am not an expert on all possible number of ambersands but an even number of ambersands appears to be the problem.               
                                                                                                                                      
    PROBLEM:                                                                                                                          
    ========                                                                                                                          
                                                                                                                                      
    This issues a warning                                                                                                             
    =====================                                                                                                             
                                                                                                                                      
    %ymdel nope / nowarn;                                                                                                             
                                                                                                                                      
    %put &nope ;                                                                                                                      
    WARNING: Apparent symbolic reference NOPE not resolved.                                                                           
    &nope                                                                                                                             
                                                                                                                                      
                                                                                                                                      
    But this does not                                                                                                                 
    =================                                                                                                                 
                                                                                                                                      
    %let var1=foo ;                                                                                                                   
    %let i=1 ;                                                                                                                        
                                                                                                                                      
    %put &&&var&i                                                                                                                     
                                                                                                                                      
    Rule '&&' become '&' so                                                                                                           
                                                                                                                                      
    &var&i does not exist                                                                                                             
                                                                                                                                      
    var&i does exist                                                                                                                  
                                                                                                                                      
                                                                                                                                      
    *                _     _                                                                                                          
     _ __  _ __ ___ | |__ | | ___ _ __ ___                                                                                            
    | '_ \| '__/ _ \| '_ \| |/ _ \ '_ ` _ \                                                                                           
    | |_) | | | (_) | |_) | |  __/ | | | | |                                                                                          
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|                                                                                          
    |_|                                                                                                                               
    ;                                                                                                                                 
                                                                                                                                      
    * problem - even number of ambersands(4);                                                                                         
    * should issue a warning but does not;                                                                                            
                                                                                                                                      
    %symdel i j var1 / nowarn;                                                                                                        
                                                                                                                                      
    %let var1=foo ;                                                                                                                   
    %let i=1 ;                                                                                                                        
                                                                                                                                      
    %put &&&var&i;                                                                                                                    
                                                                                                                                      
    * FYI this is ok = odd number of ambersands(3);                                                                                   
                                                                                                                                      
    %symdel i j var1 / nowarn;                                                                                                        
                                                                                                                                      
    %let var1=foo ;                                                                                                                   
    %let i=1 ;                                                                                                                        
                                                                                                                                      
    %put &&var&i;                                                                                                                     
                                                                                                                                      
    * problem - even number of ambersands(8);                                                                                         
    * should issue a warning but does not;                                                                                            
                                                                                                                                      
    %symdel i j var1 / nowarn;                                                                                                        
                                                                                                                                      
    %let j=1;                                                                                                                         
    %let i=j;                                                                                                                         
    %let var1=10 ambersands;                                                                                                          
    %put &&&&&var&&&i;                                                                                                                
                                                                                                                                      
    * But this is ok;                                                                                                                 
    * Odd7 ambersands;                                                                                                                
                                                                                                                                      
    %symdel i j var1 / nowarn;                                                                                                        
                                                                                                                                      
    %let j=1;                                                                                                                         
    %let i=j;                                                                                                                         
    %let var1=10 ambersands;                                                                                                          
    %put &&&&var&&&i;                                                                                                                 
                                                                                                                                      
    *_                             _       _   _                                                                                      
    | |___      _____    ___  ___ | |_   _| |_(_) ___  _ __  ___                                                                      
    | __\ \ /\ / / _ \  / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|                                                                     
    | |_ \ V  V / (_) | \__ \ (_) | | |_| | |_| | (_) | | | \__ \                                                                     
     \__| \_/\_/ \___/  |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/                                                                     
                                                                                                                                      
    ;                                                                                                                                 
                                                                                                                                      
    %macro resolvem(mac);                                                                                                             
                                                                                                                                      
      %let rc=%sysfunc(dosubl(%nrstr(                                                                                                 
          data _null_;                                                                                                                
            res = ifc(mod(countc(&mac,'&'),2)=0,"Warning"," ");                                                                       
            put res;                                                                                                                  
          run;quit;                                                                                                                   
      )));                                                                                                                            
                                                                                                                                      
    %mend resolvem;                                                                                                                   
                                                                                                                                      
    %resolvem('&&&var&i');                                                                                                            
                                                                                                                                      
    WARNING                                                                                                                           
                                                                                                                                      
    %resolvem('&&var&i');                                                                                                             
                                                                                                                                      
    /* no output */                                                                                                                   
                                                                                                                                      
    %resolvem('&&&&var&&&i');                                                                                                         
                                                                                                                                      
    /* no output */                                                                                                                   
                                                                                                                                      
    %resolvem('&&&&&var&&&i');                                                                                                        
                                                                                                                                      
                                                                                                                                      
    * one liner (too much Klingon for me);                                                                                            
                                                                                                                                      
    %put %sysfunc(ifc(%sysfunc(mod(%sysfunc(countc('&&&&&var&&&i','&')),2))=0,Warning,));                                             
                                                                                                                                      
    WARNING                                                                                                                           
                                                                                                                                      
