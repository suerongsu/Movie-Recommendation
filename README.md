# Movie-Recommendation
> d<-read.table("D:/movie_recommendation/movie_users_rate.csv",header=TRUE,sep=',')
#Transpose and check data
> library(reshape)
> movie<-melt(d,id=c("User"))
> head(movie)
> movie<-rename(movie,c("User"="MovieID","variable"="User","value"="Rating"))
> head(movie)
  MovieID  User Rating
1  movie1 user1      5
2  movie2 user1      3
3  movie3 user1      4
4  movie4 user1      3
5  movie5 user1      3
6  movie6 user1      5
> summary(movie)
> d1<-read.table("D:/movie_recommendation/user_rate.csv",header=TRUE,sep=',')
> movie$Rating[movie$Rating==0]<-NA
> moviettl<-rbind(movie,d1)
> movierec<-cast(moviettl,User~MovieID,value="Rating")
> movierec$User<-NULL
# Use recommenderlab package in R#
> library(recommenderlab)
> class(movierec)<-"data.frame"
> movierec<-as.matrix(movierec)
> mov.rating<-as(movierec,"realRatingMatrix")
> recommenderRegistry$get_entries(dataType="realRatingMatrix")
$IBCF_realRatingMatrix
Recommender method: IBCF
Description: Recommender based on item-based collaborative filtering (real data).
Parameters:
   k method normalize normalize_sim_matrix alpha na_as_zero minRating
1 30 Cosine    center                FALSE   0.5      FALSE        NA

$PCA_realRatingMatrix
Recommender method: PCA
Description: Recommender based on PCA approximation (real data).
Parameters:
  categories method normalize normalize_sim_matrix alpha na_as_zero minRating
1         20 Cosine    center                FALSE   0.5      FALSE        NA

$POPULAR_realRatingMatrix
Recommender method: POPULAR
Description: Recommender based on item popularity (real data).
Parameters: None

$RANDOM_realRatingMatrix
Recommender method: RANDOM
Description: Produce random recommendations (real ratings).
Parameters: None

$SVD_realRatingMatrix
Recommender method: SVD
Description: Recommender based on SVD approximation (real data).
Parameters:
  categories method normalize normalize_sim_matrix alpha treat_na minRating
1         50 Cosine    center                FALSE   0.5   median        NA

$UBCF_realRatingMatrix
Recommender method: UBCF
Description: Recommender based on user-based collaborative filtering (real data).
Parameters:
  method nn sample normalize minRating
1 cosine 25  FALSE    center        NA

> mov.recommModel<-Recommender(mov.rating[1:943],method="IBCF")
> mov.recommModel
Recommender of type ‘IBCF’ for ‘realRatingMatrix’ 
learned using 943 users.
#Predict recommendations Top 20#
> mov.predict<-predict(mov.recommModel,mov.rating[944],n=20)
> mov.predict
Recommendations as ‘topNList’ with n = 20 for 1 users. 
> as(mov.predict,"list")
[[1]]
 [1] "movie1039" "movie1051" "movie1101" "movie114"  "movie1147" "movie117"  "movie1196" "movie121"  "movie1235"
[10] "movie124"  "movie1243" "movie129"  "movie1293" "movie1301" "movie136"  "movie137"  "movie1401" "movie1448"
[19] "movie1467" "movie15"  
#Predict recommendations Top 5#
> mov.predict<-predict(mov.recommModel,mov.rating[944],n=5)
> as(mov.predict,"list")
[[1]]
[1] "movie1039" "movie1051" "movie1101" "movie114"  "movie1147"
