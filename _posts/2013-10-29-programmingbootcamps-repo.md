---
layout: post
title: ProgrammingBootcamps.com Repository Pattern
description: 
category: Portfolio
tags: [Repository Pattern, Code, Microsoft]
image:
  feature: picture-29.jpg
comments: true
share: true
---

At Coder Camps, I have been taught a pattern that they call the Adapter/Interface pattern, but really this is called the Repository pattern in most of the rest of the programming world. When I built my personal project at the end of my camp experience, I decided to play around with the repository pattern a bit more, and stray from the same fileanames and folders we were taught to use.

### Repository Pattern

Instead of calling my interfaces using the name Adapter, I used the word Repository and named my files to match. `ProgrammingBootcamps.com.Data/IBootcampedioRepository.cs` and `BootcampedioRepository`. The interface defines empty method signatures:

{% highlight c# %}
{% raw %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using ProgrammingBootcamps.com.Data.Models;

namespace ProgrammingBootcamps.com.Data
{
    public interface IBootcampedioRepository
    {
        IQueryable<Bootcamp> GetBootcamps();
        IQueryable<Bootcamp> GetBootcampById(int id);
        Bootcamp GetBootcampById_IncludingCategoriesReviews(int id);
        IQueryable<Bootcamp> GetBootcampsIncludingReviews();
        IQueryable<Bootcamp> GetBootcampById_IncludingReviews(int id);
        IQueryable<BootcampReview> GetBootcampReviews();
        IQueryable<BootcampReview> GetBootcampReviewsByBootcamp(int bootcampId);
        IQueryable<Category> GetCategories();

        bool Save();
        bool AddBootcamp(Bootcamp newBootcamp);
        bool AddReview(BootcampReview newReview);
    }
}
{% endraw %}
{% endhighlight %}

Now inside `ProgrammingBootcamps.com.Data/IBootcampedioRepository.cs` I place the code that actually does the heavy lifting in .Net using Entity framework to interact with the context. The context is the object that deals with pulling rows of data into DbSets for retreival, manipulation, and saving back to the database.

I seperated several of my calls from the methods so that I could call them seperately and catch errors if they failed.

{% highlight c# %}
using System.Data.Entity;
using ProgrammingBootcamps.com.Data.Models;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ProgrammingBootcamps.com.Data
{
    public class BootcampedioRepository : IBootcampedioRepository
    {
        private BootcampedioContext _ctx;
        public BootcampedioRepository(BootcampedioContext ctx)
        {
            _ctx = ctx;
        }
        public IQueryable<Bootcamp> GetBootcamps()
        {
            return _ctx.Bootcamps;
        }

        public IQueryable<Bootcamp> GetBootcampById(int id)
        {
            var bootcamp = _ctx.Bootcamps.Where(b => b.Id == id);
            return bootcamp;
        }

        public Bootcamp GetBootcampById_IncludingCategoriesReviews(int id)
        {
            Bootcamp bootcamp = new Bootcamp();
            bootcamp = _ctx.Bootcamps.Find(id);
            bootcamp.Categories = _ctx.Categories.Where(c => c.Id == bootcamp.CategoryId).ToList();
            bootcamp.BootcampReviews = _ctx.BootcampReviews.Where(b => b.BootcampId == bootcamp.Id).ToList();
            return bootcamp;
        }

        public IQueryable<Bootcamp> GetBootcampsIncludingReviews()
        {
            return _ctx.Bootcamps.Include("BootcampReviews");
        }

        public IQueryable<Bootcamp> GetBootcampById_IncludingReviews(int id)
        {
            return _ctx.Bootcamps.Include("BootcampReviews").Where(b => b.Id == id);
        }

        public IQueryable<BootcampReview> GetBootcampReviewsByBootcamp(int bootcampId)
        {
            return _ctx.BootcampReviews.Where(r => r.BootcampId == bootcampId);
        }

        public IQueryable<Category> GetCategories()
        {
            return _ctx.Categories;
        }

        public IQueryable<BootcampReview> GetBootcampReviews()
        {
            return _ctx.BootcampReviews;
        }


        //Try-Catches for Adding and Saving Bootcamps and Reviews :: Need to add logging on errors

        public bool Save()
        {
            try
            {
                return _ctx.SaveChanges() > 0;
            }
            catch (Exception ex)
            {
                //TODO log this error
                return false;
            }
        }

        public bool AddBootcamp(Bootcamp newBootcamp)
        {
            try
            {
                _ctx.Bootcamps.Add(newBootcamp);
                return true;
            }
            catch (Exception ex)
            {
                // TODO log this error
                return false;
            }
        }
        
        public bool AddReview(BootcampReview newReview)
        {
            try
            {
                _ctx.BootcampReviews.Add(newReview);
                return true;
            }
            catch (Exception ex)
            {
                // TODO log this error
                return false;
            }
        }

    }
}

{% endhighlight %}

<figure>
	<a href="{{ site.url }}/images/programbootcampbrowse.png"><img src="{{ site.url }}/images/programbootcampbrowse.png"></a>
	<figcaption><a href="http://www.programmingbootcamps.com/" data-toggle="tooltip" title="Visit Programming Bootcamps">The final product when it retreives bootcamps through the repository pattern</a>.</figcaption>
</figure>