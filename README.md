# Angular2 WordPress API Service

[![npm version](https://badge.fury.io/js/ng2-wp-api.svg)](https://badge.fury.io/js/ng2-wp-api) [![Build Status](https://travis-ci.org/MurhafSousli/ng2-wp-api.svg?branch=master)](https://travis-ci.org/MurhafSousli/ng2-wp-api)

![Alt text](/assets/logo.png?raw=true "Optional Title")

## Purpose

This library is designed to make it easy for your Angular2 application to request specific resources from a WordPress install. it returns the API server's response to your application as an `Observable<Response>` object.

## Requirments

Wordpress site and WP API v2 plugin activated.

## Installing

Install it with npm

`npm install ng2-wp-api --save`

## Usage

### Abstract

The library offers the following services:

 - `WpCollection` Service to get a collection of objects.
 - `WpModel` Service to get a single object.
 - `WpState` Service to get and set the library's configurations.

```
    WpCollection
     ├── currentPage: number
     ├── totalPages: number
     ├── totalObjects: number
     |
     ├── get(args: any): Observable<Response>
     ├── more(): Observable<Response>
     ├── hasMore(): boolean

    WpModel
     ├── get(id: number, args?: any): Observable<Response>
     ├── add(body): Observable<Response>
     ├── update(id: number, body): Observable<Response>
     ├── delete(id: number): Observable<Response>

    WpState
     ├── getBaseUrl(): string
     ├── setBaseUrl(url: string): void
     ├── getAuthKeys(): string
     ├── setAuthKeys(username: string, password: string): void

```

**Default Endpoints** are : `Posts, Pages, Users, Categories, Tags, Taxonomies, Statuses, Comments, Media`


### Initialization

First of all, we must initialize the library's configuration in `WpState` service, set the `baseUrl` to the wordpress host address (should be used within the root component).

`WpState.setBaseUrl('YourWordPressBaseUrl')`

Inject `WORDPRESS_PROVIDERS` in the root component provider or directly in bootstrap, because we only want one global instance of `WpState`.


in App component (root):
```
@Component({
    selector: 'app',
    providers:[WORDPRESS_PROVIDERS],
    ...
})
export class App {
    constructor(wpState: WpState){
        wpState.setBaseUrl("http://yourWordPressDomain.com");
    }
}
```
[Initilizing WP Service example](/examples/Initilizing WP Service.ts)

### Using the service in your component

Now the service is initialized, the services `WpModel`, `WpCollection` become ready in any component, note that contrary to what we did with `WpState`, Every component uses the services (`WpModel` or `WpCollection`) must has its own instance of the service, therefore we must inject the service within each component uses the service.


### Examples:

#### Getting Collection

Getting a collection of posts

```
args: QueryArgs;

constructor(private service:WpCollection) { 

}

ngOnInit(){

    /** Filtering Collections, more info https://codex.wordpress.org/Class_Reference/WP_Query#Parameters */
    this.args = new QueryArgs();

    /** Get 6 posts per page */
    this.args.per_page = 6; 
    this.args._embed = true;
    this.fetchPosts();
}

fetchPosts(){
    this.service.Posts().get(this.args).subscribe(
        (res) => {
            this.posts = res;
        },
        err => {
            /** handle errors */
            console.log(err)
        }
    );
}
```
[Getting Single Post example](/examples/Getting Collection.ts)

#### Getting Single Post

Getting a single post by ID

*PS: when embed is set to true, you will get featured image, categories, tags and author with the response.*

```
id: string;
args: QueryArgs;
post: Post;

constructor(private service:WpModel) {

}

ngOnInit(){
    /** Assume we already have the post ID */
    this.id = 7;

    /** Set the query arguments, more info https://codex.wordpress.org/Class_Reference/WP_Query#Parameters */
    this.args = new QueryArgs();
    this.args._embed = true;
    this.fetchPost();
}

fetchPost() {
    this.service.Posts().get(this.id, this.args).subscribe(
        (res) => {
            this.post = new Post(res);
        },
        /** Handle request error */
        err => console.log(err)
    );
}
```
[Getting Single Post example](/examples/Getting Single.ts)

Note that before requesting any data from the service, Pass the endpoint to the service `service.setEndpoint($endpoint: string | WpHelpler.Endpoint)`, endpoint can be type of `string` or `WpHelper.Endpoint`.
For **Custom Routes** pass the endpoint as string e.g. `service.setEndpoint("myplugin/v1") `

## Authentication

To use Add/Update/Delete functions, user must be authenticated. Encode and store the user credentials using `WpState.setAuthKeys($username, $password)`.
This will add the user credentials to the headers of any request.

```
@Component({
    selector: 'app',
    providers:[WORDPRESS_PROVIDERS],
    ...
})
export class App {
    constructor(wpState: WpState){
        wpState.setBaseUrl("http://yourWordPressDomain.com");
        wpState.setAuthKeys("yourUsername", "yourPassword");
    }
}
```
[Initializing WpService example](/examples/Initilizing WP Service.ts)

## Issues

If you identify any errors in this service, or have an idea for an improvement, please open an [issue](https://github.com/MurhafSousli/ng2-wp-api/issues). I am excited to see what the community thinks of this project, and I would love your input!

## License

[MIT](/LICENSE)

