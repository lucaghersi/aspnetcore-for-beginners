The tutorial below is based on [*"Get started with ASP.NET Core Razor Pages in Visual Studio Code"*](https://docs.microsoft.com/en-us/aspnet/core/tutorials/razor-pages-vsc/razor-pages-start) from docs.microsoft.com.

### Prerequisites
* [.NET Core SDK 2.1](https://www.microsoft.com/net/download/) 
*  [Visual Studio Code](https://code.visualstudio.com/?wt.mc_id=adw-brand&gclid=Cj0KCQjwqYfWBRDPARIsABjQRYwLe3b9dJMixA98s8nS8QfuNBKGsiRVRXzB93fe4E27LGK5KLrGcnYaAgdREALw_wcB)
* Tutorial 1- [Create a Razor Page application](https://github.com/dotnet-presentations/aspnetcore-for-beginners/blob/master/Tutorial/1-Create%20a%20Razor%20Page/Create-a-Razorpage.md)
* Tutorial 2- [Add a Model](https://github.com/dotnet-presentations/aspnetcore-for-beginners/blob/master/Tutorial/2-Add%20a%20model/Addamodel.md)
* Tutorial 3 - [Update Page](https://github.com/dotnet-presentations/aspnetcore-for-beginners/tree/master/Tutorial/3-Upate%20Pages)

## Adding Search to a page 

In this quick tutorial we are going to search to the Index Page. By the end of this tutorial you can search by genre and name.

- Update the Index page's `OnGetAsync` method

*Add code below to Movies/Index.cshtml*
```
@{
    Layout = "_Layout";
}
```
*Then edit the Movies/Index.cshtml.cs*
```
public async Task OnGetAsync(string searchString)
{
    var movies = from m in _context.Movie
                 select m;

    if (!String.IsNullOrEmpty(searchString))
    {
        movies = movies.Where(s => s.Title.Contains(searchString));
    }

    Movie = await movies.ToListAsync();
}
```
**Test search string**
- Run your application 
- Append the query string to the end `?searchString=Wrinkle`

![](https://github.com/dotnet-presentations/aspnetcore-for-beginners/blob/master/Tutorial/4-Add%20Search/images/searchString.PNG)

### Add a Search Box
**Search by Title** 
Open the Index.cshtml file and add the`<form>`
```
<h2>Index</h2>

<p>
    <a asp-page="Create">Create New</a>
</p>
<form>
    <p>
        Movie Title:<input type="text" name="SearchString">
         <input type="submit" value="Filter"/>
    </p>
</form>
```
- run the application `http://localhost:5000/movies` 
- Enter a film title 

![](https://github.com/dotnet-presentations/aspnetcore-for-beginners/blob/master/Tutorial/4-Add%20Search/images/form.PNG)

**Search by genre**
- Add the code below to Pages/Movies/Index.cshtml.cs

*Note you will need to add `using Microsoft.AspNetCore.Mvc.Rendering;`*
```
public class IndexModel : PageModel
{
    private readonly RazorPagesMovie.Models.MovieContext _context;

    public IndexModel(RazorPagesMovie.Models.MovieContext context)
    {
        _context = context;
    }

    public IList<Movie> Movie;
    public SelectList Genres;
    public string MovieGenre { get; set; }
```

- Update `OnGetAsync` method
```
public async Task OnGetAsync(string movieGenre,string searchString)
        {
            IQueryable<string> genreQuery = from m in _context.Movie
                                    orderby m.Genre
                                    select m.Genre;

            var movies = from m in _context.Movie
                        select m;

            if (!String.IsNullOrEmpty(searchString))
            {
                movies = movies.Where(s => s.Title.Contains(searchString));
            }

            if (!String.IsNullOrEmpty(movieGenre))
            {
                movies = movies.Where(x => x.Genre == movieGenre);
            }
            Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
            Movie = await movies.ToListAsync();
        }
```
-  Update Index.cshtml
```
<form>
    <p>
        <select asp-for="MovieGenre" asp-items="Model.Genres">
            <option value="">All</option>
        </select>
        
        Movie Title:<input type="text" name="SearchString">
         <input type="submit" value="Filter"/>
    </p>
</form>
```
- run the application `http://localhost:5000/movies`

![](https://github.com/dotnet-presentations/aspnetcore-for-beginners/blob/master/Tutorial/4-Add%20Search/images/genre.PNG)

Mission Accomplished 

![](https://media.giphy.com/media/3o6UBbrfvYwldawfDi/giphy.gif)

You've have built your first Razor Page application