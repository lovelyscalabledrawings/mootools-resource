Mootools ORM (like ActiveResource or Jester).

Readme below may be a little bit outdated. To be rewritten.




  var Post = new Resource("Post")
  Post.find("all", function(p) {
    p.title = "All your base are belong to us"
    p.save
  })

  p = new Post({title: "Hello world"}).save
  p.errors #=> {something}

This thing is rails & merb-oriented
As they are supported out of the box:

Merb, with Merb.disable(:json) & ActiveRecord:

  provides :json
  display @post, :only => [:title, :body, :created_at, :id], :include => {:comments => {:only => [:body, :created_at, 
  :id]}}

Merb without ActiveRecord:

  provides :json
  display @post #cant control anything here, associations not included

Rails:

  respond_to do |f|
    f.json do
      @post.to_json, :only => [:title, :body, :created_at, :id], :include => {:comments => {:only => [:body, 
      :created_at, :id]}}
    end
  end

So with that backend we can do this: 

  new Resource("Post", {
    associations: {
      comments: ["Comment", {prefix: true}] #setting autoprefix on 
    } 
  })

  Post.find(1).chain(function(post) {
    post.title = "defaced"
    post.comments.each(function(comment) {
      comment.destroy() # DELETE /posts/1/comments/n
    })
    post.save().chain(function(result) {
      if (!result) {
        alert(post.errors) # => ["Title is too short"]
      } else {
        alert("Ha ha ha... You are on your way to destruction")
      }
    }) # PUT /posts/1/ title=defaced
  })