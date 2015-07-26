---
layout: post
title: "Rails route 用法"
category: RoR
excerpt: 在Rails中，route 很重要。
tags: rails
---

**默认路由**：

~~~ruby
  match '/:controller(/:action(/:id))'  
~~~
**正则路由**： 

~~~ruby 
  match 'products/:id', :to => 'catalog#view'  
~~~
**命名路由**： 

~~~ruby 
  match 'logout', :to => 'sessions#destroy', :as => 'logout'  
~~~
**根路由**： 

~~~ruby 
  root :to => 'welcome#show'  
~~~

### 路由简写技巧：

:to 键的省略: 

~~~ruby
  match 'account' => 'account#index'  
~~~

相当于：

~~~ruby 
  match 'account', :to => 'account#index'  
  match 'info' => 'projects#info', :as => 'info'  
~~~

*注意*：

:as `在rails3中是改变 helper， 在rails2中是改变 path`

当路径和控制器(及action)一至时，可省略指派控制器部分 

~~~ruby
  match 'account/overview'  
~~~

相当于： 

~~~ruby
  match 'account/overview', :to => 'account#overview'  
~~~
**Verb路由**:

当需要限制http请求方法的时候通过键 :via ，也可以直接把方法写在最前面： 

~~~ruby  
  get 'account/overview' 
~~~

相当于：

~~~ruby
  match 'account/overview', :via => 'get'  
  match 'account/setup', :via => [:get, :post]  
  # 支持get\post\put\delete四种HTTP方法  
~~~

**resources路由**： 

~~~ruby
  resources :posts, :except => [:index]  
  resources :posts, :only => [:new, :create]  
  edit_post GET /posts/:id/modify(.:format) {:controller=>"posts", :action=>"edit"}  
  resources :posts, :path_names => { :edit => 'modify' }   

  resources :projects do  
    resources :tasks, :people  
  end  

  resources :products do  
    collection do  
      get  :sold  
      post :on_offer, :search  
    end  
    get :buy, :on => :member  
    post :batch, :on => :collection  
  end  

  resource :session do  
    get :create  
  end  
~~~

###\:shallow用法： 

Rails3中的shallow用法与Rails2中一致 

~~~ruby
  resources :blogs, :shallow => true do  
    resources :comments  
  end  
~~~

####使用\:shallow前后相同部分：

	blog_comments GET /blogs/:blog_id/comments(.:format) {:controller=>"comments", :action=>"index"}
	blog_comments POST /blogs/:blog_id/comments(.:format) {:controller=>"comments", :action=>"create"}
	new_blog_comment GET	/blogs/:blog_id/comments/new(.:format) {:controller=>"comments", :action=>"new"}
	blogs GET /blogs(.:format) {:controller=>"blogs", :action=>"index"}
	blogs POST /blogs(.:format) {:controller=>"blogs", :action=>"create"}
	new_blog GET /blogs/new(.:format) {:controller=>"blogs", :action=>"new"}
	edit_blog GET /blogs/:id/edit(.:format) {:controller=>"blogs", :action=>"edit"}
	blog GET /blogs/:id(.:format) {:controller=>"blogs", :action=>"show"}
	blog PUT /blogs/:id(.:format) {:controller=>"blogs", :action=>"update"}
	blog DELETE /blogs/:id(.:format) {:controller=>"blogs", :action=>"destroy"}	

####使用:shallow前后*不同*部分： 

不使用shallow选项： 

	edit_blog_comment GET /blogs/:blog_id/comments/:id/edit(.:format) {:controller=>"comments", :action=>"edit"}
	blog_comment GET /blogs/:blog_id/comments/:id(.:format) {:controller=>"comments", :action=>"show"}
	blog_comment PUT /blogs/:blog_id/comments/:id(.:format) {:controller=>"comments", :action=>"update"}
	blog_comment DELETE /blogs/:blog_id/comments/:id(.:format) {:controller=>"comments", :action=>"destroy"}


使用shallow选项后： 

	edit_comment GET /comments/:id/edit(.:format) {:controller=>"comments", :action=>"edit"}
	comment GET /comments/:id(.:format) {:controller=>"comments", :action=>"show"}
	comment PUT /comments/:id(.:format) {:controller=>"comments", :action=>"update"}
	comment DELETE /comments/:id(.:format) {:controller=>"comments", :action=>"destroy"}

可以看出使用shallow选项后，对于已经存在的资源使用简化方式操作，具体行为涉及到 edit\show\update\destroy 四种 
另外，shallow选项的有效范围是对自身及嵌套的资源都有效，如下面这个例子： 

~~~ruby
  resources :publishers do  
    resources :magazines do  
      resources :albums, :shallow => true do  
        resources :photos do  
          resources :images  
        end  
      end  
    end  
  end  
~~~
这个例子中 albums、photos、images 都会使用简化方式，而 magazines 不会。特别注意：这种嵌套方式极不推荐，一般嵌套的层级最好不要超过一级 

### scope路由 
:path 改变Path，:module 改变Controller, :name_prefix || :as 改变  helper 

~~~ruby
  scope 'admin' do  
    resources :posts  
  end 
~~~

#### 相当于： 

~~~ruby
  scope :path => 'admin' do  
    resources :posts  
  end  
~~~
**生成路由**： 

	posts GET /admin/posts(.:format) {:controller=>"posts", :action=>"index"}
	posts POST /admin/posts(.:format) {:controller=>"posts", :action=>"create"}
	new_post GET /admin/posts/new(.:format) {:controller=>"posts", :action=>"new"}
	edit_post GET /admin/posts/:id/edit(.:format) {:controller=>"posts", :action=>"edit"}
	post GET /admin/posts/:id(.:format) {:controller=>"posts", :action=>"show"}
	post PUT /admin/posts/:id(.:format) {:controller=>"posts", :action=>"update"}
	post DELETE /admin/posts/:id(.:format) {:controller=>"posts", :action=>"destroy"}

~~~ruby
  scope :module => 'admin' do  
    resources :posts  
  end 
~~~

#### 相当于： 

~~~ruby
  resources :posts, :module => 'admin'  
~~~
**生成路由**：

	posts GET /posts(.:format) {:controller=>"admin/posts", :action=>"index"}
	posts POST /posts(.:format) {:controller=>"admin/posts", :action=>"create"}
	new_post GET /posts/new(.:format) {:controller=>"admin/posts", :action=>"new"}
	edit_post GET /posts/:id/edit(.:format) {:controller=>"admin/posts", :action=>"edit"}
	post GET /posts/:id(.:format) {:controller=>"admin/posts", :action=>"show"}
	post PUT /posts/:id(.:format) {:controller=>"admin/posts", :action=>"update"}
	post DELETE /posts/:id(.:format) {:controller=>"admin/posts", :action=>"destroy"}

~~~ruby
  scope :name_prefix => 'admin' do  
    resources :posts  
  end  
~~~

#### 相当于：

	resources :posts, :name_prefix => 'admin'  

**生成路由**：

	admin_posts GET /posts(.:format) {:controller=>"posts", :action=>"index"}
	admin_posts POST /posts(.:format) {:controller=>"posts", :action=>"create"}
	new_admin_post GET /posts/new(.:format) {:controller=>"posts", :action=>"new"}
	edit_admin_post GET /posts/:id/edit(.:format) {:controller=>"posts", :action=>"edit"}
	admin_post GET /posts/:id(.:format) {:controller=>"posts", :action=>"show"}
	admin_post PUT /posts/:id(.:format) {:controller=>"posts", :action=>"update"}
	admin_post DELETE /posts/:id(.:format) {:controller=>"posts", :action=>"destroy"}

~~~ruby
  scope 'admin', :module => 'admin', :name_prefix => 'admin' do  
    resources :posts  
  end  
~~~

#### 相当于：

~~~ruby
  namespace 'admin' do   
    resources :posts  
  end  
~~~
**生成路由**：

	admin_posts GET /admin/posts(.:format) {:controller=>"admin/posts", :action=>"index"}
	admin_posts POST /admin/posts(.:format) {:controller=>"admin/posts", :action=>"create"}
	new_admin_post GET /admin/posts/new(.:format) {:controller=>"admin/posts", :action=>"new"}
	edit_admin_post GET /admin/posts/:id/edit(.:format) {:controller=>"admin/posts", :action=>"edit"}
	admin_post GET	/admin/posts/:id(.:format) {:controller=>"admin/posts", :action=>"show"}
	admin_post PUT	/admin/posts/:id(.:format) {:controller=>"admin/posts", :action=>"update"}
	admin_post DELETE /admin/posts/:id(.:format) {:controller=>"admin/posts", :action=>"destroy"}


### 在路由中定义跳转：

~~~ruby
  match "/posts/github" => redirect("http://github.com/rails.atom")  
  # 地址 /foo/1 会自动跳转到 /bar/1s  
  match "/foo/:id", :to => redirect("/bar/%{id}s")    
  # /account/proc/inosin 会自动跳转到 /inosins  
  match 'account/proc/:name', :to => redirect {|params|   
  # "/#{params[:name].pluralize}" }  
  match "/stories" => redirect {|p, req| "/posts/#{req.subdomain}" }   
~~~

### 路由中的限制： 

##### 限制 id 只能为数字 

~~~ruby
  match "/posts/show/:id", :to => "posts#index", :id => /\d+/  
  match "/posts/show/:id", :to => "posts#index", :constraints => {:id => /\d+/}  
~~~

##### 限制子域名  

~~~ruby
  match "photos", :constraints => {:subdomain => "admin"}   
~~~

##### 限制访问者 IP 

~~~ruby
  constraints(:ip => /127.0.0.1/) do  
    match  '/questions', :to => redirect("http://www.stackoverflow.com/")  
  end  
~~~

##### 当访问者 ip 是 192.168.1.* 的来访者访问 子域名为 "test"  

~~~ruby
  match "/ttt" => proc{|env| [200, {}, ["hello test"]]}, :constraints => {:subdomain => "test", :ip => /192.168.1.\d+/
  }   
~~~

#### 路由通配符： 

~~~ruby
  resources :photos, :id => /\d+/  
  match 'photos/*other' => 'photos#unknown'  
~~~

#####上面这两行路由则会把不符合7种path的其他url全部解析到PhotoController#unknown中去处理，params[:other]可得到path中/photos/之后的部分，注意这两行的顺序不能颠倒  

~~~ruby
  match 'books/*section/:title' => 'books#show'   
~~~

例如：`books/some/section/last-words-a-memoir` 中 

~~~ruby
  params[:section] = "some/section", 
  params[:title] = "last-words-a-memoir".  
  match '*a/foo/*b' => 'test#index'   
~~~

例如：`zoo/woo/foo/bar/baz` 中 params[:a] = "zoo/woo", params[:b] = "bar/baz"  

~~~ruby
	# Rack: 
 
  match "/foo", :to => proc {|env| [200, {}, ["Hello world"]] }  
  match 'rocketeer.js' => ::TestRoutingMapper::RocketeerApp  
  RocketeerApp = lambda { |env| 
    [200, {"Content-Type" => "text/html"}, ["javascripts"]]  
  }  
~~~

*参考文档*： 

<http://www.engineyard.com/blog/2010/the-lowdown-on-routes-in-rails-3/>

<http://www.railsdispatch.com/posts/rails-routing>

<http://guides.rails.info/routing.html> 

<http://asciicasts.com/episodes/203-routing-in-rails-3> 

<http://asciicasts.com/episodes/231-routing-walkthrough>

<http://asciicasts.com/episodes/232-routing-walkthrough-part-2>

