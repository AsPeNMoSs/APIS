# CHARATERISTICS OF THE APIS

## RICK AND MORTY

The Rick and Morty API has three main objects that you can request. Episodes, Characters, and Locations.

For my implementation I focused on the Characters. In order to request a character you must use the base url [`https://rickandmortyapi.com/api/character/`](https://rickandmortyapi.com/api/character/)

If you just call that URL, you get a list of all of the characters. In order to return just one character you must follow the URL with the ID of that character.

For example [`https://rickandmortyapi.com/api/character`](https://rickandmortyapi.com/api/character/)`/2` Returns

```
{
  "id": 2,
  "name": "Morty Smith",
  "status": "Alive",
  "species": "Human",
  "type": "",
  "gender": "Male",
  "origin": {
    "name": "Earth",
    "url": "https://rickandmortyapi.com/api/location/1"
  },
  "location": {
    "name": "Earth",
    "url": "https://rickandmortyapi.com/api/location/20"
  },
  "image": "https://rickandmortyapi.com/api/character/avatar/2.jpeg",
  "episode": [
    "https://rickandmortyapi.com/api/episode/1",
    "https://rickandmortyapi.com/api/episode/2",
    // ...
  ],
  "url": "https://rickandmortyapi.com/api/character/2",
  "created": "2017-11-04T18:50:21.651Z"
}
```

You can also get multiple characters by following the URL with a list of numbers ex: 1,2,3 or \[1,2,3].

If you want to return a random character you would have to use math.rand to first get a random number, and then put that number into the URL.

For my implementation I created a listview, and included the first 20 characters in it, because all 493 would be too laggy.

My interface to call the character used a path variable number, so that I could call each specific ID for each character

```
@GET ("character/{number}")
Call<Character> getCharacterByNumber (@Path("number") String number);
```

I used a for loop to make the call 20 times, and used the i variable to call the character

```
for(int i = 0; i < 19;i++) {
    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl(CharacterService.BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .build();

    CharacterService characterService = retrofit.create(CharacterService.class);
    characterNumber = i;
```

When adding characters to the listview, make sure that you add it to the adapter listview, so that you don’t rely on having the information before you receive it. EX:

By directly adding it to the list in the charadterAdapter, I don’t have to rely on a list with a certain length when using the adapter.

## POKE API

## API Reference <a href="#api-reference" id="api-reference"></a>

Pokédex API is based on a single layer, a HTTPS/REST consumption-only API that only allows the `GET` method to consume all the Pokémon information you’ll ever need.

## Base URL <a href="#base-url" id="base-url"></a>

The base URL for all API requests is:

```
https://pokeapi.glitch.me
```

## API Versioning <a href="#api-versioning" id="api-versioning"></a>

Pokédex API exposes different versions of the API. You can specify version by including it in the request path:

```
https://pokeapi.glitch.me/v{version_number}
```

Omitting the version number from the route will route requests to the current default version. We recommend specifying a version number in the request path so that your app doesn’t run into incompatibility when the default version is changed.V

| Version | Status    | Default |
| ------- | --------- | ------- |
| 1       | Available | ✔       |

## Authentication <a href="#authentication" id="authentication"></a>

No authentication is required to access this API. All the resources are and available to everyone. However, we will implement authentication methods in the future that will give authenticated users higher rate limits or no rate limits at all (more on [rate limits](broken-reference) later).

## User Agent <a href="#user-agent" id="user-agent"></a>

Applications and websites using the API must provide a valid [User Agent](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.43) which specifies information about the app and website, in the following format:

```http
User-Agent: AppName ($url, $version)
```

Applications and websites may append more information and metadata to the end of this string as they wish.

### Example User Agent Header <a href="#example-user-agent-header" id="example-user-agent-header"></a>

```http
User-Agent: BastionDiscordBot (https://bastionbot.org, 6.16.1)
```

## Rate Limiting <a href="#rate-limiting" id="rate-limiting"></a>

This API implements a process for limiting and preventing excessive requests in accordance with [RFC 6585](https://tools.ietf.org/html/rfc6585#section-4).

This API rate limits requests in order to prevent abuse and overload of our services. Rate limits are applied on a global basis, spanning across the entire API.

Because we may change rate limits at any time and rate limits can be different per application, rate limits should not be hard coded into your application or website. In order to properly support our dynamic rate limits, your application or website should parse for our rate limits in response headers and locally prevent exceeding of the limits as they change.

{% hint style="info" %}
Current rate limit is 500 API calls in 12 hours. And we will increase the rate limits every time we upgrade our server for better performance.
{% endhint %}

We recommend that your application or website should locally cache the data that you receive to save the number of API calls consumed for the same resource.

### Header Format <a href="#header-format" id="header-format"></a>

For every API request made, we return optional HTTP response headers containing the rate limit encountered during your request.

```http
Retry-After: 1301000
X-RateLimit-Limit: 500
X-RateLimit-Remaining: 201
X-RateLimit-Reset: 905212800000
```

`Retry-After` - The number of milliseconds after which the rate limit will reset. It is returned only if you’ve been rate limited.

`X-RateLimit-Limit` - The number of requests that can be made

`X-RateLimit-Remaining` - The number of remaining requests that can be made

`X-RateLimit-Reset` - Epoch time (seconds since 00:00:00 UTC on January 1, 1970) at which the rate limit resets. It is returned only if you’ve been rate limited.

### Exceeding A Rate Limit <a href="#exceeding-a-rate-limit" id="exceeding-a-rate-limit"></a>

In the case that a rate limit is exceeded, the API will return a HTTP 429 response code with a JSON body.

| Field        | Type    | Description                                                           |
| ------------ | ------- | --------------------------------------------------------------------- |
| error        | integer | The HTTP response code.                                               |
| message      | string  | A message saying you are being rate limited.                          |
| retry\_after | integer | The number of milliseconds to wait before submitting another request. |

Note that the normal rate-limiting headers will be sent in this response.

{% hint style="warning" %}
API users that regularly hit and ignore rate limits will be banned (by their IP address) from using the API again.
{% endhint %}

### Example Rate Limit Response <a href="#example-rate-limit-response" id="example-rate-limit-response"></a>

The rate-limiting response will look something like the following:

```bash
< HTTP/1.1 429 TOO MANY REQUESTS
< Content-Type: application/json
< Retry-After: 14400000
< X-RateLimit-Limit: 500
< X-RateLimit-Remaining: 0
< X-RateLimit-Reset: 905212800000
{
  "error": 429,
  "message": "You are being rate limited. You're way too spicy at catching Pokémon.",
  "retry_after": 14400000
}
```
