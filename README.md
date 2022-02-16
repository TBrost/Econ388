# Econ388






AIRFARE.DES

year      origin    destin    id        dist      passen    fare      bmktshr  
ldist     y98       y99       y00       lfare     ldistsq   concen    lpassen  

  Obs:      4596

 1. year                             1997, 1998, 1999, 2000
 2. origin                           flight's origin
 3. destin                           flight's destination
 4. id                               route identifier
 5. dist                             distance, in miles
 6. passen                           avg. passengers per day
 7. fare                             avg. one-way fare, $
 8. bmktshr                          fraction market, biggest carrier
 9. ldist                            log(distance)
10. y98                              =1 if year == 1998
11. y99                              =1 if year == 1999
12. y00                              =1 if year == 2000
13. lfare                            log(fare)
14. ldistsq                          ldist^2
15. concen                           = bmktshr
16. lpassen                          log(passen)
-- Added Variables --
17. LargeShare                       =1 if bmktshr > 0.75
18. y97                              =1 if year == 1997
19. distsq                           =dist^2
