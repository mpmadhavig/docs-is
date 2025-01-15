---
template: templates/complete-guide.html
heading: Securing Routes within the app
read_time: 2 min
---

In a .NET Blazor Web Application, handling and securing routes is an important aspect of managing user access, ensuring the right content is displayed to the right users, and protecting sensitive resources.

Blazor uses the `RouteView` component and a built-in routing system to manage navigation within the application. Routes are defined using the `@page` directive at the top of a Razor component (.razor file). You can also navigate to different routes programmatically using the `NavigationManager` service, which we will also cover in this guide when securing the routes.

Blazor provides the `AuthorizeView` component to conditionally display UI elements based on the user's authentication and authorization state. This can be used to control access to specific sections of your Blazor UI. In this case, we want to protect the User Claims page, in addition to the `Counter` and `Weather` pages (both of which are present out-of-the-box).

Let’s first navigate to the `NavMenu.razor` file under the `/Components/Layout` directory and add the `AuthorizeView` component to encapsulate the `Counter` and `Weather` components. Then we can add the User Claims menu item as well.

```html title="NavMenu.razor"
<AuthorizeView>
    <div class="nav-item px-3">
        <NavLink class="nav-link" href="counter">
            <span class="bi bi-plus-square-fill-nav-menu" aria-hidden="true"></span> Counter
        </NavLink>
    </div>

    <div class="nav-item px-3">
        <NavLink class="nav-link" href="weather">
            <span class="bi bi-list-nested-nav-menu" aria-hidden="true"></span> Weather
        </NavLink>
    </div>

    <div class="nav-item px-3">
        <NavLink class="nav-link" href="user-claims">
            <span class="bi bi-list-nested-nav-menu" aria-hidden="true"></span> User Claims
        </NavLink>
    </div>
</AuthorizeView>
```

We will be using the `Authorize` attribute to protect the routes for the above three pages so that only authenticated users can access them. If an unauthorized user attempts to access these routes, they should be redirected to the login page of the Identity Provider.

First, add the `Authorize` attribute to the `Counter.razor`, `Weather.razor`, and `UserClaims.razor` pages just above the `PageTitle` attribute as shown below.

```csharp title="Counter.razor"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize]
```

In order to redirect unauthorized users to the login page, we will create a Razor component named `RedirectToLogin.razor` under the `/Components` directory and add the following code. We will use the `NavigateTo` method from the built-in `NavigationManager` to perform the redirection.

```csharp title="RedirectToLogin.razor"
@inject NavigationManager Navigation

@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo($"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}", forceLoad: true);
    }
}
```

Now, open the `Routes.razor` file and add the `AuthorizeRouteView` component as shown below, which is used to protect entire pages or routes. It only renders the associated route if the user is authorized. Otherwise, it can redirect the user to a login page using the `RedirectToLogin` component we created.

```html title="Routes.razor"
<Router AppAssembly="typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="routeData" DefaultLayout="typeof(Layout.MainLayout)">
            <NotAuthorized>
                <RedirectToLogin />
            </NotAuthorized>
        </AuthorizeRouteView>
        <FocusOnNavigate RouteData="routeData" Selector="h1" />
    </Found>
</Router>
```

By following these steps, you can easily secure your Blazor application routes and manage user access based on their authentication state.

You can also start the application at this point and observe the homepage of the application with the changes we made.

![Application Home page after changes]({{base_path}}/complete-guides/dotnet/assets/img/image6.png){: width="800" style="display: block; margin: 0;"}

Clicking on the login button will initiate an OIDC request. You will be able to observe the authorize request in the browser devtools. To see this, right click on the application and click inspect and switch to the network tab. In the filter input, type “authorize”, and then click on the Login button.

!!! tip "Tip"

    The OpenID Connect specification offers several functions, known as grant types, to obtain an access token in exchange for user credentials. This example uses the authorization code grant type. In this process, the app first requests a unique code from the authentication server, which can later be used to obtain an access token. For more details on the authorization code grant type, please refer to the [Asgardeo documentation.](https://wso2.com/asgardeo/docs/guides/authentication/oidc/implement-auth-code-with-pkce/){:target="_blank"} 

Asgardeo will receive this authorization request and respond by redirecting the user to a login page to enter their credentials.

At this stage, **you need to create a [Test user in Asgardeo](https://wso2.com/asgardeo/docs/guides/users/manage-users/#onboard-users){:target="_blank"}  to try out the application.** Once you create a test user, you can enter the username and password of the test user to the login screen.

If the login is successful, you should be able to see the application as shown below.

![Application Home page after authentication]({{base_path}}/complete-guides/dotnet/assets/img/image7.png){: width="800" style="display: block; margin: 0;"}

Now that we secured the routes within the application, in the next step we will implement the functionality to access a protected API using the access token obtained during the authentication process.
