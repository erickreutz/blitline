Getting Started
===============

You must first have a Blitline.com account to successfully use the gem. You can obtain one (free) by going to http://www.blitline.com

Once you have your account, you will need to find you APPLICATION_ID which you can get by logging in and clicking on the *Account* tab.


In you application environment, install the Blitline gem or add the Blitline gem to your Gemfile

    $ gem install blitline

or...if you have a Gemfile

    gem 'blitline'

*Note: We have changed our recommended method of submitting Blitline jobs to just submitting job hashes instead of building
objects through an object model. This is because the Blitline API changes and evolves faster than people can/want to update their gems. 
Old ways will still work, they will just not be used in documentation going forward. Since Blitline works via a JSON API, it is
easier to understand the hierarchy of the job when viewed as a hash. (You can find some old object based documentation [here](https://github.com/blitline-dev/blitline/wiki))*

Learn more about [job hashes](http://www.blitline.com/docs/api) and see some related JSON [examples](http://www.blitline.com/docs/examples)

## The new preferred method is as follows:

Once the gem is installed, you can start a Rails console and try the following:

    $ blitline_service = Blitline.new
    $ blitline_service.add_job_via_hash({
          "application_id"=>"YOUR_APP_ID",
          "src"=>"http://cdn.blitline.com/filters/boys.jpeg",
          "functions"=>[
              {
                  "name"=>"resize_to_fit",
                  "params"=>{
                      "width"=>100
                  },
                  "save"=>{
                      "image_identifier"=>"MY_CLIENT_ID"
                  }
              }
          ]
      })
    $ blitline_service.post_jobs

The resulting JSON will look something like:

```js
{"results":{"images":[{"image_identifier":"MY_CLIENT_ID", "s3_url":"http://s3.amazonaws.com/blitline/9393939393/99/6CPGskk11mM-B8zaCYUJzqbw.jpg"}] ,"job_id":"4JVyFJBIhlpHNXLK-YClq5g"}}
```

This JSON contains:

- Any error information that may have happened with the submit
- A list of images
  - Each image has an `image_indentifier`, which is the `image_identifier` you used in the `save` params.
  - Each image also has an `s3_url` which is the final destination of the image (once it is done processing).

### Important! ###
This result does not indicate that the job is done! The job has been put on a queue and will be done shortly. The best
way to identify when the job is completed is by adding a `postback_url` to the job hash and we will call back that url
when we have completed the image processing.

As an alternative to blitline_service.post_jobs, you can use blitline_service.post_job_and_wait

    $ blitline_service.post_job_and_wait_for_poll

Which will block, and using Blitline's [long polling](http://www.blitline.com/docs/polling) functionality, return when the job is completed. There must be only one requested job. The returned result will look like

```js
{"original_meta"=>{"width"=>720, "height"=>540}, "images"=>[{"image_identifier"=>"MY_CLIENT_ID", "s3_url"=>"http://s3.amazonaws.com/blitline/2013082822/20/7J6Izja0hkG7rvNj-MUJDfQ.jpg", "meta"=>{"width"=>100, "height"=>75}}], "job_id"=>"9hgxoQ10WI7YN2QcioUarbA"}
```

This JSON contains:
- Any error information associated with the job
- Metadata associated with the original image
- A list of images processed.

In fact, this result will contain all the exact same information a Blitline postback would contain.

The example above is a trivial (and pretty uninteresting) demonstration of how to use the Blitline gem. You can find documentation about Blitline.com and it's services by following the links below

* [Quickstart](http://www.blitline.com/docs/quickstart)
* [Blitline Blog](http://blitline.tumblr.com)
* [See all the available functions](http://www.blitline.com/docs/functions)

