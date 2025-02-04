# Managing Access Tokens

Instafeed.js requires an access token to be able to fetch images from Instagram and this token must be kept fresh because it expires after 60 days.

You can use one of the following methods to manage your access tokens:

- [instatoken-refreshers](#instatoken-refreshers)
- [Instant-tokens.com](#instant-tokenscom)

## History

In 2019, Instagram launched the [Instagram Basic Display API](https://developers.facebook.com/blog/post/2019/10/15/launch-instagram-basic-display-api/) and announced they would be sunsetting their Legacy API, which powered the 1.0 version of Instafeed.js, in 2020. As of December of 2024 the Basic Display API has been deprecated and the [Instagram API](https://developers.facebook.com/docs/instagram-platform/instagram-api-with-instagram-login) in now being used. This new API actually uses the same routes as the Basic Display API but uses a different account registration method.

In the Legacy API, access tokens had no expiration time, so Instafeed.js was originally designed to have users hard-code their access tokens (which were read-only) in the browser-side code of their webpages. However, when the Basic Display API was initially launched, access tokens were short-lived, and only valid for 1 hour. This posed a challenge for Instafeed.js, since the library relied on those tokens being hard-coded.

In 2020 however, Instagram [added support for long-lived access tokens](https://developers.facebook.com/blog/post/2020/01/14/instagram-basic-display-api-long-lived-access-tokens-available/), opening up an migration path for Instafeed.js.

These long-lived access tokens did come with a caveat, which is that it would require _some_ amount of server-side code to keep the tokens refreshed past their 60-day lifetime.

## instatoken-refreshers

[Instatoken Refreshers](https://github.com/jasper-clarke/instatoken-refreshers) is a collection of tools that can be used to manage your Instagram access tokens.

1. [instatokend](#instatokend)
2. [instatoken-ga](#instatoken-ga)

### instatokend

[Instagram Token Daemon](https://github.com/jasper-clarke/instatoken-refreshers/tree/instatokend) is a Golang powered daemon that can manage multiple Instagram accounts and keep their tokens up-to-date with custom refresh times and the option to manually refresh tokens.

Pros:

- Open Source
- Entirely cross-platform binary
- Can be deployed to any hosting platform

Cons:

- Needs to be run on a server with read/write access to the filesystem

The configuration file must be named `config.json` and be placed in the same directory as the executable.

Example configuration file:

```json
{
  "port": "8080",
  "refresh_freq": "daily", // daily, weekly, monthly, test(every 5 minutes)
  "my-instagram-1": {
    "token": "mysecrettoken"
  },
  "my-instagram-2": {
    "token": "another-secret-token"
  }
}
```

Below is a minimal example of using Instafeed.js with the `instatokend` daemon:
As you can see you can perform a GET request to the `/token/{account}` endpoint to get the access token for a specific account.

```html
<!-- Include Instafeed.js here -->

<div id="instafeed"></div>

<script type="text/javascript">
  async function getInstagramToken() {
    try {
      const response = await fetch(
        // Your instatokend server URL
        "http://123.456.789.100:8080/token/my-instagram-1",
      );
      const data = await response.json();
      return data.token;
    } catch (error) {
      console.error("Error fetching token:", error);
      throw error;
    }
  }

  getInstagramToken()
    .then((token) => {
      const feed = new Instafeed({
        accessToken: token,
      });
      feed.run();
    })
    .catch((error) => {
      console.error("Error setting up Instagram feed:", error);
    });
</script>
```

### instatoken-ga

[Instatoken GA](https://github.com/jasper-clarke/instatoken-refreshers/tree/instatoken-ga) is a GitHub Action that can be used to automatically refresh your Instagram access tokens.

Pros:

- Open Source
- Deployed and Managed on GitHub

## Instant-tokens.com

[Instant-tokens.com](https://www.instant-tokens.com) is a website that generates & refreshes Instagram access tokens. It is provided for free by [Coding Badger](https://codingbadger.com), and is easy to integrate with Instafeed.js.

Pros:

- No setup required, just sign in with your Instagram account.

Cons:

- Paid (Monthly or Annually Subscription)
- Closed-source
- Owned & operated by 3rd party (Coding Badger)
