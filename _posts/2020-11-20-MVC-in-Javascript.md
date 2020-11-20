---
title: MVC in Javascript
permalink: /blog/2020/11/20/mvc_in_javascript/
categories:
  - Uncategorized
published: true
---

I was working on my side project [JustRead](https://justread.io) and at some point, the complexity 
of the client side Javascript code became so high that I felt need for a more modular code.

Initially, I had lots of server side calls scattered all around the place and was changing DOM everywhere.

So, the first though was "**MVC**"! This is a very good design pattern to manage a system with ralatively complex data structures.

But as this was a pet project, I did not want a full-featured MVC framework. Something like a light mini-framework was enough for me.

I did search but could not find anything that could suit my requirements. So what I did, was that I implemented a minimal, single file MVC blueprint.

This is not a framework, meaning you cannot copy the file to your project and attach it to your existing code, but it gives you a good starting point to include
MVC in your project.

One of my initial confusions was about responsibilities. Which part is responsible for what? Which part should update DOM? Which part should handle a button click? 
Which part should update server about a change in the UI?

So after reading lots of resources, I can summarize my findings as below (although they are not hard rules):

1. **Model**: This holds internal state of the model + supports events that can be fired to sync this state and what we have on the server side (e.g. row deletion or refresh).
2. **View**: This is responsible to render model into the UI (DOM) and also captures UI events. But when these events are captured, it only delegates them to its own event listeners (Controller here).
3. **Controller**: This is the glue that binds View and Model. It handles events from both sides and redirects them to the other side or makes method calls to do the job.

So, this is an example of a Model (I use jQuery event model). For the sake of simplicity and clarity, I have only included a few events, 
but you can have any number of events as you need:

```javascript
function Model() {
    this.events = $({});
    this.state = {};

    this.refresh = function() {
        var self = this;

        body = { "searchText": this.state.searchText,
                 "startIndex": this.state.startIndex,
                 "endIndex": this.state.endIndex
        };

        httpPost("api/inbox.jr?", body, function(data, xhr) {
            if ( xhr.status == 204 ) return;

            self.state = data;
            sessionStorage.setItem('modelState', JSON.stringify(self.state));
            self.events.trigger('modelRefreshed', [self.state] );
        });
    }

    this.init = function() {
        if ( sessionStorage.getItem('modelState') != null ) {
            this.state = JSON.parse(sessionStorage.getItem('modelState'));
        }
    }
    
    this.likeToggled = function(id, liked) {
        var self = this;
        httpPost("api/likeToggled.jr", { id: id, liked: liked },
            function(data) {
                var row = self.state.rows.find(x => x.id = id);
                row.liked = liked;
                self.events.trigger('rowUpdated', row);
            });
    }

    this.search = function(phrase) {
        this.state.searchText = phrase;
        //When we do a new search, start/end index are reset
        this.state.startIndex = 1;
        this.state.endIndex = 20;

        this.refresh();
    }
}
```

As you can see, this is where server communication happens. Any change in the data either from server side (initial load) or from client side (search, ...) is handled here.

This is a sample of View. Note that View is responsible for UI rendering and even capturing, so its logic is highly dependent on how you arrange and design you front end:

```javascript
function View() {
    this.events = $({});

    this.render = function(model) {
        var self = this;
        //empty table and insert table rows
        $("#emailTable > tbody").empty();
        model.rows.forEach(x => this.renderRow(x))

        $("#emailTable > tbody td.likeColumn i").click(function(event) {
            event.stopPropagation();
            var liked = $(this).css('opacity') != 1.0;
            var id = $(this).closest('tr').attr('id');
            self.events.trigger('likeToggled', [id, liked]);
        });
    };

    this.init = function() {
        var self = this;

        $('#cmdRefresh').click(function() {
            self.events.trigger('reload');
        });

        $('#cmdDelete').click( function() {
            var idSelector = function() { return $(this).closest('tr').attr('id'); };
            var selectedEmails = $("td.checkbox input[type='checkbox']:checked").map(idSelector).get();
            if ( selectedEmails.length == 0 ) return;

            self.events.trigger('delete', [ selectedEmails ]);
        });

        $("#txtSearch").on('keyup', delay(function () {
            self.events.trigger('search', $(this).val());
        }, 250));
    }

    this.renderRow = function(row) {
        var id = row.id;
        var source = row.senderName ?? row.senderEmail;
        var title = row.subject;
        var date = row.prettyTimestamp;
        var src = row.senderEmail.substring(row.senderEmail.indexOf('@')+1, row.senderEmail.length);

        source = source.replace(/^(\")/,"");
        source = source.replace(/(\")$/,"");

        var rowTemplate = `<tr id=${id} class="${row.read ? 'read':'unread'}">
            <td class="likeColumn">
                <i class="fas fa-lg fa-heart" style="opacity: ${row.liked ? 1.0:0.1}"></i>
            </td>
            <td class="sourceIcon">
                <img class="sourceIcon" src="assets/img/sources/${src}.png" width="24" height="24">
            </td>
            <td class="source" title="${source}"  data-title="Source">${source}
            </td>
            <td class="title" data-title="Title">${title}
            </td>
            <td class="date" title="${row.timestampStr}" data-title="Date">${date}
            </td>
        </tr>`;

        $('#emailTable').find('tbody').append(rowTemplate);
        $("#"+id+" .sourceIcon").on('error', function() {
            $(this).attr('src', "assets/img/sources/source-not-available.svg");
        });
    }

    this.updateRow = function(row) {
        var tr = $('#' + row.id);

        if ( row.liked ) {
            tr.find('td.likeColumn i').fadeTo(150, 1.0);
        } else {
            tr.find('td.likeColumn i').fadeTo(150, 0.1);
        }
    }
}
```

And finally Controller, where Model and View talk to each other indirectly:

```javascript
var controller = {
    model: new Model(),
    view: new View(),
    
    init: function() {
        self = this;
        this.model.events.on('modelRefreshed', function(event, data) {
            //tell view to render this
            self.view.render(data);
        });

        this.view.events.on('likeToggled', function(event, id, liked) {
            self.model.likeToggled(id, liked);
        });

        this.view.events.on('reload', function(event) {
            self.model.refresh();
        });

        this.view.events.on('search', function(event, phrase) {
            self.model.search(phrase);
        });

        //e.g. when a row is liked
        this.model.events.on('rowUpdated', function(event, row) {
            self.view.updateRow(row);
        });

        this.model.events.on('rowsDeleted', function(event) {
            //deleting a row from UI is not enough because we will not be populating the page fully
            self.model.refresh();
        });

        this.model.init();
        this.model.refresh();
        this.view.init();
    }
};
```

I have decided to use a variable for controller and function objects for model and view.

The only remaining part is to call `controller.init` upon page load and it will handle all the rest.

Note that this blueprint has minimum constraints on your design. You can have any number of events and methods as you want depending on your situation.

Have you ever used MVC in your front-end? Did you have to use an existing framework, 
like [Backbone](https://backbonejs.org/), [Ember](https://emberjs.com/) or [Angular](https://angularjs.org/)? Or you had to write your own mini-framework?

