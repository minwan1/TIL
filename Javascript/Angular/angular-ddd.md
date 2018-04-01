```javascript
/**
 * OOP in AngularJS
 *
 * The following implements some pseudo code for a Video Blog Application using both AngularJS'
 * Dependency Injection and 'Classical' Javascript Prototypal Inheritance.
 *
 * Recently I've decided to use a more 'Classical' Javascript design pattern. My reasoning for this
 * decision was based mostly on 2 factors:
 *
 * 1. Using classes allows you to create code that is reusable and DRY. This is especially helpful
 *    when working on a large codebases.
 *
 * 2. I work on a team that includes iOS and Android developers. Since we are building different
 *    variations of the same product, it makes sense to use a similar concepts and vocabulary cross
 *    platform as much as possible.
 *
 */

/**
 * Before I begin to discuss the pattern in which I believe to be the most suitable,
 * here is the most intuitive way to implement classes with AngularJS.
 **/

var App = angular.module('App', []);


/**
 * Post Class
 */

App.factory('Post', [function() {

  function Post(title, author) {
    this.title = 'hello';
    this.author = 'ddluc32';
    this.date = new Date();
    likesCount = 0;
    comments = [];
  }

  Post.prototype.like = function() {
    this.likesCount++;
  }

  Post.prototype.addComment = function(comment) {
    this.comments.push(comment)
  }

  return Post;

}]);

/**
 * Feed Class
 */

App.factory('Feed', ['$http', '$q', 'Post', function($http, $q, Post) {

  function Feed() {
    this.posts = [];
  }

  Feed.prototype.addPost = function(title, author) {
    var newPost = new Post(title, author);
    this.posts.push(newPost);
  }

  Feed.prototype.loadFeed = function() {

    var loadingFeed = $q.defer();

    $http.get('api.blog.com/posts')
      .then(function(apiResponse) {

        if (api.error) loadingFeed.reject(api.error);

        else {

          for (var i=0, posts = apiResponse.posts.length; i < length; i++) {
            var newPost = new Post(apiResponse.posts[i].title, apiResponse.posts[i].author)
            this.posts.push(newPost);
          }

          loadingFeed.resolve();
        }

      });

    return loadingFeed.promise;

  }

  return Feed;

}]);


/**
 * Seems like a reasonable approach, but this pattern has a gotcha.
 */

 var newPost = new Post(title, author);

 /**
  * Since we created a new instance of the Post class inside of the Feed class, it becomes impossible
  * to mock the Post object when writing unit tests for the Feed class.
  *
  * Realistically, you should probably be writing unit tests for application, so this is not a viable
  * option. Even if you do not plan on writing unit tests, this seems to be an anti-pattern to AngularJS.
  *.Depency injection is at the core of AngularJS, thus i think this approach is both inconsiderate and
  * against good practice.
  *
  */

  /**
   * My solution to this problem
   */

  App.factory('Post', [function() {
    return {
      create: function(title, author) {

        function Post(title, author) {
          this.title = 'hello';
          this.author = 'ddluc32';
          this.date = new Date();
          likesCount = 0;
          comments = [];
        }

        Post.prototype.like = function() {
          this.likesCount++;
        }

        Post.prototype.addComment = function(comment) {
          this.comments.push(comment)
        }

        return new Post(title, author);

      }
    }
   }]);

   App.factory('Feed', ['$http', '$q', 'Post', function($http, $q, Post) {

     function Feed() {
       this.posts = [];
     }

     Feed.prototype.addPost = function(title, author) {
       var newPost = Post.create(title, author);
       this.posts.push(newPost);
     }

     Feed.prototype.loadFeed = function() {

       var loadingFeed = $q.defer();

       $http.get('api.blog.com/posts')
         .then(function(apiResponse) {

           if (api.error) loadingFeed.reject(api.error);

           else {

             for (var i=0, posts = apiResponse.posts.length; i < length; i++) {
               var newPost = Post.create(apiResponse.posts[i].title, apiResponse.posts[i].author)
               this.posts.push(newPost);
             }

             loadingFeed.resolve();
           }

         });

       return loadingFeed.promise;

     }

     return Feed;

   }]);

   /**
    * Funcionally, it's the same. However, now we can easily spy on the Post.create() function
    * and respond with a mocked object in our unit test!
    */

```
angularjs dto[https://medium.com/opinionated-angularjs/advanced-routing-and-resolves-a2fcbf874a1c]
[https://gist.github.com/ddluc/c8aa1eb5ab6c65a3623c]
