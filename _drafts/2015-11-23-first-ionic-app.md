---
layout:     post
title:      Build a Chat App with Ionic 
date:       2015-11-23
summary:    Use the ionic framework and Firebase to build a chatboards app.
categories: ionic javascript
---

## What you will learn

The goals of the tutorial are:

* To create a new ionic app from the command line utility (CLI).
* To learn how to use ionic UI components and create new components to create a chat app UI.
* To write a new ionic/AngularJS controller and service for the app.
* To use Firebase to add a persistent backend to your application.

## Requirements

If you haven't yet, you must first install [node.js](https://nodejs.org/en/) as well as a good text editor.

After installing node.js, run `npm install -g ionic cordova` to install ionic and cordova CLI tools. 

## 1. Starting a new project

Let's start a new app using the ionic command-line tool. Navigate to the appropriate directory where you'd like to build your app, and run the following command: 

`ionic start <app-name> tabs`

This command will scaffold the structure of a new ionic app and include the required dependencies. In this command, `tabs` refers to the type of app to scaffold; the tabs template will create a new app with three tabs for navigation. Other templates include `blank`, and `sidemenu`. 

Next, let's setup the project to use Sass. Sass is a superset of CSS that give more flexibility and features, but gets compiled down to CSS for the browser to run. The ionic CLI gives a handy command to help you set this feature up automatically: 

`ionic setup sass`

After running that, you can run the following command to see your application in your browser: 

`ionic serve --lab`

## 2. Creating the layouts with mock-data

Update the mock Chats service in `www/js/services.js` to: 

{% highlight javascript %}
angular.module('starter.services', [])

.factory('Chats', function() {

  // Some fake testing data
  var data = 
    {
      "boards": {
        "ionic-seattle": {
          "metadata": {
            "board": "ionic-seattle",
            "lastUpdated": 12345678,
            "lastMessage": {
              "from": "class",
              "body": "Doing well.",
              "timestamp": 12345678
            }
          },
          "messages": [{
              "from": "zack",
              "body": "Hi, everyone. How're you doing?",
              "timestamp": 12345678
            }, {
              "from": "class",
              "body": "Doing well.",
              "timestamp": 12345678
            }
          ]
        },
        "banter": {
          "metadata": {
            "board": "banter",
            "lastUpdated": 12345678,
            "lastMessage": {
              "from": "vivian",
              "body": "Get a new joke.",
              "timestamp": 12345678
            }
          },
          "messages": [{
              "from": "zack",
              "body": "Why did the chicken cross the road?",
              "timestamp": 12345678
            }, {
              "from": "vivian",
              "body": "Get a new joke.",
              "timestamp": 12345678
            }
          ]
        }
      }
    }

  var dataAsArray = [{
      "metadata": {
        "board": "ionic-seattle",
        "lastUpdated": 12345678,
        "lastMessage": {
          "from": "class",
          "body": "Doing well.",
          "timestamp": 12345678
        }
      }
    }, {
      "metadata": {
        "board": "banter",
        "lastUpdated": 12345678,
        "lastMessage": {
          "from": "vivian",
          "body": "Get a new joke.",
          "timestamp": 12345678
        }
      }
    }
  ]

  return {
    allChats: allChats,
    getChat: function(board) {
      return {
        messages: messages(board),
        sendMessage: sendMessage(board)
      };
    }
  }

  function allChats() {
    return dataAsArray;
  }

  function sendMessage(board) {

    return function(user, body) {
      var timestamp = Date.now();
      var message = {
        body: body,
        from: user,
        timestamp: timestamp
      };

      data.boards[board].messages.push(message);
    }
  }

  function messages(board) {
    return function() {
      return data.boards[board].messages;
    }
  }
});
{% endhighlight %}


To use the new mock data service calling pattern, let's update the controllers in `www/js/controllers.js`. The `ChatsCtrl` is simple, we just need to pass the view the list of all chat boards.

{% highlight javascript %}
.controller('ChatsCtrl', function($scope, Chats) {
  $scope.chats = Chats.allChats();
})
{% endhighlight %}

The `ChatsDetailCtrl` will have two objects passed to the view: the name of the chat, all of the message objects in the chat.

{% highlight javascript %}
.controller('ChatDetailCtrl', function($scope, $stateParams, Chats) {
    var chatId = $stateParams.chatId;
    var chat = Chats.getChat(chatId);

    $scope.name = chatId;
    $scope.messages = chat.messages();
})
{% endhighlight %}

Now, we must update the views to match the new view-models. 

Update the `www/templates/tab-chats.html` template to match the Chats view-model.

{% highlight html %}
{% raw %}
<ion-view view-title="Chats">
  <ion-content>
    <ion-list>
      <ion-item class="item-icon-right" 
                ng-repeat="chat in chats" 
                type="item-text-wrap" 
                href="#/tab/chats/{{chat.metadata.board}}">
        <h2>{{ chat.metadata.board }}</h2>
        <p>{{ chat.metadata.lastMessage.body }}</p>
        <p>{{ chat.metadata.lastMessage.from }}</p>
        <i class="icon ion-chevron-right icon-accessory"></i>
      </ion-item>
    </ion-list>
  </ion-content>
</ion-view>
{% endraw %}
{% endhighlight %}

Update the `www/templates/chat-detail.html` template to match the ChatsDetail view-model.

{% highlight html %}
{% raw %}
<ion-view view-title="{{ name }}">
  <ion-content class="padding">
      <ul class="messages">
        <li ng-repeat="message in messages">
          <p>{{ message.body }}</p>
          <p><i class="icon ion-person"></i>{{ message.from }}</p>
        </li>
      </ul>
  </ion-content>
</ion-view>
{% endraw %}
{% endhighlight %}

Run `ionic serve --lab` to see the new changes in the browser.

## 3. Adding style to layouts

Let's add some styling to the messages list in the ChatsDetail view. In `scss/ionic.app.scss` add the following Sass code:

{% highlight css %}
ul.messages{
  padding-left: 0;
  list-style: none;
  li{
    color: white;
    background: #3C7EFD;
    margin: 0 0 3px 0;
    border-radius: 5px 25px 25px 5px;
    padding: 10px 15px;
    width: auto;
    display: table;
    &:first-child{
      border-radius: 25px 25px 25px 5px;
    }
    &:last-child{
      border-radius: 5px 25px 25px 25px;
    }
    p {
    	margin: 0 0 5px 0;
    }
  }
}

.chat-item em {
	font-style: italic;
}
{% endhighlight %}


Run `ionic serve --lab` to see the new changes in the browser.

## 4. Replace mock data service with Firebase

##### Create Firebase app
To complete this step you must create free Firebase account at [firebase.com](https://firebase.com/). Create a new application, and give it a new unique name/url. Copy that URL, you'll need that in the next step.

##### Add Firebase references
Next, to use Firebase in our app, we need to add a reference to the client libraries in the head of `www/index.html`.

{% highlight html %}
<!-- include FireBase -->
<script src="https://cdn.firebase.com/js/client/2.2.4/firebase.js"></script>
<script src="https://cdn.firebase.com/libs/firebase-util/0.2.5/firebase-util.min.js"></script>
<script src="https://cdn.firebase.com/libs/angularfire/1.1.2/angularfire.js"></script>
{% endhighlight %}

##### Update Chats service to use Firebase
Now, update the Chats service in `www/js/services.js` to use a Firebase backend, rather than local mock data. Replace the `FBURL` constant value with your app's Firebase url that you created above.

{% highlight javascript %}
angular.module('starter.services', ['firebase'])

.constant('FBURL', '<!-- Enter Firebase URL here -->')

.factory('Chats', function(FBURL, $firebaseArray, $firebaseObject) {
  var root = new Firebase(FBURL);
  var boardsRef = root.child('boards');

  return {
    allChats: allChats,
    getChat: function(board) {
      return {
        messages: messages(board),
        sendMessage: sendMessage(board)
      };
    }
  }

  ////

  function allChats() {
    var collection = 
      new Firebase.util.NormalizedCollection([boardsRef, 'boards'])
        .select('boards.metadata');

    return $firebaseArray(collection.ref());
  }

  function sendMessage(board) {
    var boardRef = boardsRef.child(board);

    return function(user, body) {
      var timestamp = Date.now();
      var message = {
        body: body,
        from: user,
        timestamp: timestamp
      };

      // add message, update metadata
      $firebaseArray(boardRef.child('messages'))
        .$add(message)
        .then(function() {
          $firebaseObject(boardRef.child('metadata'))
            .$loaded()
            .then(function(metadata) {
              metadata.board = board;
              metadata.lastUpdated = timestamp;
              metadata.lastMessage = message;
              metadata.$save();
            });
        });
    }
  }

  function messages(board) {
    return function() {
      return $firebaseArray(
      	boardsRef.child(board).child('messages'));
    }
  }
})
{% endhighlight %}

The calling pattern of the Chats service didn't change, just the data source. So the controllers and view shouldn't need updated to view the data. However, at this point there's no data. So we need to add a few actions to create some data.

##### Create a new message board

To create a message board, we're going to use the `ionicPopup` service to prompt the user for a message board name.

Add this factory to `www/js/services.js`:

{% highlight javascript %}
.factory('BoardPopup', function ($ionicPopup) {
  return function (scope) {
    return $ionicPopup.prompt({
       title: 'New board',
       inputType: 'text',
       inputPlaceholder: "Enter name for board"
     });
  }
})
{% endhighlight %}


Now, let's use this in the controller to create a method for creating a new board. The controller now needs to have the `$state` and `BoardPopup` services injected. Then create the `newBoard` function.

{% highlight javascript %}
.controller('ChatsCtrl', function($scope, $state, Chats, BoardPopup) {

  $scope.chats = Chats.allChats();

  $scope.newBoard = function() {
    BoardPopup()
      .then(function(name) {
        if (name) {
          $state.go('^.chat-detail', { chatId: name });
        }
      })
  }
})
{% endhighlight %}

Finally, let's allow the user to select this in the UI. In `www/templates/tab-chats.html`, in the `<ion-view>` tag, preceding the `<ion-content>` add a button that calls `newBoard` in the controller when clicked.

{% highlight html %}
<ion-nav-buttons side="right">
  <button ng-click="newBoard()" 
  		  class="button button-icon icon ion-compose">
  </button>
</ion-nav-buttons>
{% endhighlight %}


##### Send a message

We need to add a controller function in the ChatDetail controller to send a message. 

{% highlight javascript %}
.controller('ChatDetailCtrl', function($scope, $stateParams, Chats) {
    var chatId = $stateParams.chatId;
    var chat = Chats.getChat(chatId);

    $scope.name = chatId;
    $scope.messages = chat.messages();

    $scope.sendMessage = function(body) {
      chat.sendMessage('user1', body);
      clearNewMessage();
    };

    function clearNewMessage() {
      $scope.message = '';
    }
})
{% endhighlight %}

Finally, in `www/templates/chats-detail.html` add an `<ion-footer-bar>` element as a sibling to `<ion-content>`. Inside of this footer, we'll use a text input element and an anchor, styled as a button, to create the message sending functionality.

{% highlight html %}
<ion-footer-bar keyboard-attach class="bar-stable item-input-inset">
  <label class="item-input-wrapper">
    <input type="text" placeholder="What's happening?" ng-model="message" />
  </label>
  <a class="button button-icon ion-paper-airplane" ng-click="sendMessage(message)"></a>
</ion-footer-bar>
{% endhighlight %}

Now we have a functioning chat boards app! 

Run `ionic serve --lab` to see the new changes in the browser.

## 5. Add User dialog

In the last step, the username "user1" was hardcoded into the `sendMessage` function. Let's create the ability to define a user name, and create a Session service to keep track of the user info. 

##### Create $localStorage service
Before setting that up, let's create a `$localStorage` service to read and write to local storage.

{% highlight javascript %} 
.factory('$localstorage', function ($window) {
  return {
    set: function(key, value) {
      $window.localStorage[key] = value;
    },
    get: function(key, defaultValue) {
      return $window.localStorage[key] || defaultValue;
    },
    setObject: function(key, value) {
      $window.localStorage[key] = JSON.stringify(value);
    },
    getObject: function(key) {
      return JSON.parse($window.localStorage[key] || '{}');
    }
  }
})
{% endhighlight %}

##### Create a NamePopup

In the same vein of the popup for creating a new chat board, let's create another popup to prompt the user for a username. 

{% highlight javascript %}
.factory('NamePopup', function ($ionicPopup) {
  return function () {
    return $ionicPopup.prompt({
       title: 'Set username',
       inputType: 'text',
       inputPlaceholder: "What's your name?"
     });
  }
})
{% endhighlight %}

##### Create Session service

Now, a `Session` service can use `$localStorage` and `NamePopup` to keep track of user info. The function `getUser` will check if a username is stored in memory, if not it will check local storage, and if that too doesn't have a username it will prompt the user for a username.

{% highlight javascript %}
.service('Session', function ($q, $localstorage, NamePopup) {
  this.setUser = function(user) {
    $localstorage.set('user', user)
    this.user = user;
  };

  this.getUser = function() {
    return $q(function (resolve, reject) {
      if (this.user) {
        resolve(this.user);
      } else if ($localstorage.get('user')) {
        this.user = $localstorage.get('user');
        resolve(this.user);
      } else {
        NamePopup().then(function (user) {
          $localstorage.set('user', user)
          this.user = user;
          resolve(user);
        });
      }
    });

  }

  this.clearUser = function() {
    this.user = null;
    $localstorage.set('user', '');
  };

  return this;
})
{% endhighlight %}

##### Prompting the user 

The last piece of this puzzle is consuming the `Session` service in the `ChatDetailCtrl`.

Inject the `Session` service into the controller.

{% highlight javascript %}
.controller('ChatDetailCtrl', function($scope, $stateParams, Chats, Session) {
{% endhighlight %}

Lastly, update the `sendMessage` function.

{% highlight javascript %}
$scope.sendMessage = function(body) {
  Session.getUser().then(function(user) {
    chat.sendMessage(user, body);
    clearNewMessage();
  })
};
{% endhighlight %}

## End

Run `ionic serve --lab` to see the functioning app in the browser.

This is a tutorial originally written for and presented at the [Ionic Seattle](http://www.meetup.com/Ionic-Seattle/) meetup.