---
title: "Delayed indexing with background_job and searchkick"
layout: post
image: /assets/images/icons8-team-643498-unsplash.jpg
headerImage: true
tag:
- Freelance
- Ruby on Rails
- Elasticsearch
category: blog
author: pete
description: How I implemented a queued indexing of updated models in a Ruby on Rails application using background_job and searchkick. Photo by Icons8 team on Unsplash
---

[Photo by Icons8 team](https://photos.icons8.com/) via [Unsplash](https://unsplash.com/photos/dhZtNlvNE8M)

## The problem

A client had problems with their search indexes being out of date compared to the latest updates to their content. Due to the distributed nature of the app a blocking solution provided a less-than optimal user experience so a more appropriate solution was required.

## The situation

The software comprises of three applications: 
1. A [Ruby on Rails](https://rubyonrails.org) JSON-speaking API
2. A [React](https://reactjs.org/) front-end for consuming the data as a user
3. A second React app for backoffice tasks.

The application is search-first in design so it's important that data is findable and accurate in the search results. With the existing implementation, reindexing may not occur until a server restart which could be some time.

The search functionality is backed by [Elasticsearch](https://www.elastic.co/products/elasticsearch) and interfaced using the [searchkick](https://github.com/ankane/searchkick) gem.

## The solution

I chose `delayed_job` as my queue manager, owing to the fact that this is a commercial project and `delayed_job` does not place any restrictions on use with the MIT license.

Following their readme, it was easy to fit to the specifics of the application. Since the application is using mongoid I needed specific support for this, which what provided with the `delayed_job_mongoid` gem.

```ruby
# Gemfile
gem 'delayed_job_mongoid'
```

Because the application is using Rails 4, it is necessary to specify the queue adapter in `application.rb`

```ruby
# config/application.rb
config.active_job.queue_adapter = :delayed_job
```

`delayed_job` needs a redis instance to manage its queues so I provided one on the default ports.

#### Queueing jobs

Now it is easy to delay any existing method by calling it through the `delay` method, like so:

```ruby
# old
Article.reindex

# new
Article.delay.reindex
```

The real data models in question have some `embedded` models which needed to also be reindexed, as their data is provided to searchkick to represent one entity in the elasticsearch index.

In order to accommodate this and to automatically queue a reindex on save for the model, add an `after_save` hook with the reindex action.

```ruby
# models/article.rb
def after_save
    self.delay.reindex
    # and any other relations that are embedded!
    # If you're using active record then ymmv
end
```

Now whenever our model is saved or updated, it'll automatically generate a job to reindex the model. Neat!

#### Consuming the queue

To recap so far there is:

1. A method to queue our long-running jobs
2. A datastore to back our job queue
3. Integration with mongoid and the `after_save` callback

The next thing is for us to actually execute the queued jobs.

In local development this is pretty straightforward. Run our rails app again but use the provided rake task to wait and listen for our jobs and take the necessary action.

In the simplest form this can be done with

```ruby
bundle exec rake jobs:work
```

The deployment environment understands `Procfile` so add a `queue` process to that and ensure it's running as part of the deployment monitoring process.

```bash
# Procfile
web: bundle exec puma -C config/puma.rb
queue: bundle exec rake jobs:work
```

### Monitoring queue health

The `rake jobs:work` task is fine, but not very chatty so it can be hard during development to ensure that jobs aren't being missed or some other problem is occurring now that we're relying on a separate worker process to do some of our work.

To ensure our queue is being consumed correctly I took a couple of steps.

Firstly in the API application I provided an endpoint to poll for the queue status. This simply returns the count of jobs in the `delayed_job` queue.

```ruby
def queue_status
    data = {
        in_queue: Delayed::Job.all.length
    }

    render json: data, status: :ok
end
```

The admin application can then poll this endpoint and report on the state of the queue to the administrator. It should be obvious if the number is high, really anything above zero for more than a few seconds, or rising that something is not right with the queue.

![Screenshot of indexing status](/assets/images/index-status.png)

Secondly for local and staging purposes including the `delayed_job_web` gem mounts a small sinatra application which provides stats on queue length, pending, processing and failed jobs.

It doesn't go into huge detail but it's a massive boost to confidence and being able to test out tasks and actions through the rails console with visual feedback.

If you're using mongoid like I am then there's an [open pull request with mongoid support](https://github.com/ejschmitt/delayed_job_web/pull/108) and you can use the proposed fork in your gemfile by targeting https://github.com/brunoporto/delayed_job_web

```ruby
# Gemfile
gem "delayed_job_web", github: 'brunoporto/delayed_job_web'
```

[Delayed job web quickstart](https://github.com/ejschmitt/delayed_job_web#quick-start-for-rails-3-and-4-applications)

### Conclusion

All in all this has increased confidence in the search indexing with visual feeback to the administrator and ensured that updates do not tie up the web server unnecessarily.

If you have a Ruby on Rails application with tricky bugs or unexpected requirements, why not [get in touch](/contact)?
