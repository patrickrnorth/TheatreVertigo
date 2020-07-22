# Live Project

## Introduction
For the last two weeks of my time at the tech academy, I worked with my peers in a team developing a full scale MVC Web Application in C#. There were some big changes that could have been a large time sink, but we used what we had to deliver what was needed on time. I saw how a good developer works with what they have to make a quality product.  Because much of the site had already been built, there were a good deal of [front end stories](#front-end-stories) and UX improvements that needed to be completed, all of varying degrees of difficulty.
  
Below are descriptions of the stories I worked on, along with code snippets and navigation links. I also have some full code files in this repo for the larger functionalities I implemented.


## Front End Stories
* [Rental Request Calendar](#rental-request-calendar-index)
* [Part Index Layout Update](#part-index-layout-update)
* [Button Stacking Bug](#button-stacking-bug)

## Rental Request Calendar Index
This page had a jQuery library called FullCalendar which is capable of CRUD functionality and displaying rental periods.

        @model IEnumerable<TheatreCMS.Models.RentalRequest>

        @{
          ViewBag.Title = "Rental Request";
        }

        <div class="container-fluid">
          <div class="m-4">

            <h2 id="RentalHeader">Rental Requests</h2>
            @*Calendar toggle button*@
            <button><i id="toggleCal" onclick="toggleIcon(this)" class="calIcon fa fa-calendar-minus"></i></button>    
            <a class="pillBtn badge badge-pill badge-danger text-black" href="@Url.Action("Create", "RentalRequest")">Create</a>
          </div>

          @*Calendar divs*@
          <div id="rentalCalendar" class="rentEventCalendar">
            <div class="calendar-body">
              <div id="rentalCalendarInner"></div>
            </div>
          </div>
        </div>


  #### Rental Request Controller JSON Method
  The Full Calendar Library required a JSON method to pass the rental request information from the server.

        public JsonResult GetRentalEvents()
        {
            var events = db.RentalRequests.ToArray();

            return Json(db.RentalRequests.Select(x => new
            {
                rentalRequestId = x.RentalRequestId,
                contactPerson = x.ContactPerson,
                company = x.Company,
                start = x.StartTime,
                end = x.EndTime,               
                projectInfo = x.ProjectInfo,
                requests = x.Requests,
                rentalCode= x.RentalCode,
                accepted = x.Accepted,
                contractSigned = x.ContractSigned,
                allDay = false,
               
            }).ToArray(), JsonRequestBehavior.AllowGet);
        }
        
  #### Rental Request CSS
  The Rental Request page required a bit of formatting to fit to the CMS's color scheme and format.
  

    /*RentalCalendar CSS*/
    .calIcon {
        font-size: 30px;
    }

    .rentEventCalendar .fc-widget-header {
        background-color: #bd1a11;
    }
    /*End RentalCalendar CSS*/
    
 
## Part Index Layout Update
I was tasked with updating the basic view for the Roles part of the theatre's website. It was previously using just a basic table layout and I updated it using bootstrap and C# with razor syntax to build a page with a description of each actor and crew member from the database. Each bootstrap card in is customized depending on what role the served on the project. 

    @model IEnumerable<TheatreCMS.Models.Part>

    @{
      ViewBag.Title = "Part";
    }

    <h2 class="ml-5 mb-3 mt-4">Part</h2>

    @if (User.IsInRole("Admin"))
    {
      <p class="ml-5 mb-3 mt-3">
        @Html.ActionLink("Create New", "Create")
      </p>
    }

    <div class="ml-1 mr-1 justify-content-center part-card-deck">
      <!--Creates a bootstrap card for every part in the production customized to the part they are reponsible for-->
      @foreach (var item in Model)
      {
        <div class="part-cardHover">
          <div class="text-light mb-3 align-items-center part-card border-white">
            <h5 class="mt-3 mb-3 underline text-center">@Html.DisplayFor(modelItem => item.Person.Name)</h5>
            <!--Check for what type of job and displays correct formatting for card.-->
            @if ((int)item.Type > 0)
            {
              //non-actor formatting
              <h6 class=" ml-4 mr-4 text-center"> @Html.DisplayFor(modelItem => item.Type)</h6>
            }
            else
            {
              //actor formatting
              <p class="font-italic text-center mb-0">Performed By:</p>
              <h6 class="mb-1 text-center"> @Html.DisplayFor(modelItem => item.Person.Name) </h6>
            }
            <p class="font-italic m-0">In the Production:</p>
            <h6 class=" ml-4 mr-4 text-center">@Html.DisplayFor(modelItem => item.Production.Title)</h6>
            <p class="bold mt-1 mb-0 text-center">Details</p>
            <p class="card-text ml-3 mr-3 part-details-elipsis">@Html.DisplayFor(modelItem => (item.Details))</p>
            <!--Card overlay for edit tools-->
            <div class="part-overlay mask d-flex align-items-end justify-content-center">
              <!--condition to look for admin authority and give access to edit options for cards-->
              @if (User.IsInRole("Admin"))
              {
                <span class="badge badge-pill badge-light m-1">@Html.ActionLink("Edit", "Edit", new { id = item.PartID })  </span>
                <span class="badge badge-pill badge-light m-1">@Html.ActionLink("Delete", "Delete", new { id = item.PartID }) </span>
                <span class="badge badge-pill badge-light m-1">@Html.ActionLink("Details", "Details", new { id = item.PartID }) </span>
              }
            </div>
          </div>
        </div>
      }
    </div>
    
#### Part Index CSS
The Part Index page required a bit of css to make sure it was inline with the format for the website along with viewable on most screen sizes.

    
    /*CSS for Admin Part View by Patrick North*/
    /*creates an elipsis if multi-line text is over 5 lines long.*/
    .part-details-elipsis {
        text-indent: 20px;
        overflow: hidden;
        text-overflow: ellipsis;
        display: -webkit-box;
        -webkit-line-clamp: 5;
        -webkit-box-orient: vertical;
    }

    .part-overlay {
        transition: .3s ease;
        opacity: 0;
        position: absolute;
        top: 50%;
        left: 50%;
        height: 100%;
        width: 100%;
        transform: translate(-50%, -50%);
        -ms-transform: translate(-50%, -50%);
        text-align: center;
        background-color: rgba(240, 77, 68, .5);
        border-radius: 25px;
    }
    /*opaque overlay for card hover*/
    .part-cardHover:hover .part-overlay {
        opacity: 1;
    }

    /*card formatting for part page*/
    .part-card {
        color: white;
        background-color: #bd1a11;
        border-radius: 25px;
        height:350px;    
        min-width: 250px;
        max-width: 280px;
        -ms-flex: 1 0 0%;
        flex: 1 0 0%;
        margin-right: 15px;
        margin-bottom: 0;
        margin-left: 15px;
        position: relative;
        display: -ms-flexbox;
        display: flex;
        -ms-flex-direction: column;
        flex-direction: column;   
        word-wrap: break-word;   
        background-clip: border-box;
        border: 1px solid rgba(0, 0, 0, 0.125);    
    }

    /*custom card-deck formatting that allows custom format for screens < 575px*/
    .part-card-deck {
        display: -ms-flexbox;
        display: flex;
        -ms-flex-flow: row wrap;
        flex-flow: row wrap;
        margin-right: -15px;
        margin-left: -15px;
    }
    /*END CSS for Admin Parts page*/

## Button Stacking Bug
This story was to fix an issue where two buttons on the navbar would get stacked when reduced to a smaller screen size. It took some time to realize the bootstrap that was assigned to the navbar was not set properly. From there, I just changed the grid layouts for the buttons that were stacking and it was fixed.

*Jump to: [Front End Stories](#front-end-stories), [Other Skills](#other-skills-learned), [Tech](#tech), [Page Top](#live-project)*

## Other Skills Learned
* Working with a group of developers to identify front end bugs to the improve usability of an application
* Improving project flow by communicating about who needs to check out which files for their current story
* Learning new efficiencies from other developers by observing their workflow and asking questions  
* Became familiar with AGILE and SCRUM methodologies using Microsoft Azure.
* Became more familiar with Entity Framework 6 Code First using MVC 5.
    
*Jump to: [Front End Stories](#front-end-stories), [Other Skills](#other-skills-learned), [Tech](#tech), [Page Top](#live-project)*

### Tech

We used many languages and methodologies in the creation of the CMS for Theatre Vertigo:

* C# and .NET Core
* jQuery
* FullCalendar Library
* Bootstrap4
* Entity Framework 6
* MVC5

*Jump to: [Front End Stories](#front-end-stories), [Other Skills](#other-skills-learned), [Tech](#tech), [Page Top](#live-project)*
