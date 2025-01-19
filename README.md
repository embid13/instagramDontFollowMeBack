# instagramDontFollowMeBack
This script shows you the people who don't follow you back, the people you don't follow back, all your followers and the people you follow.

## Steps 

### Step 1: 
Open a web browser and log in to instagram.com.

### Step 2:
Open a console: Ctrl+Shift+J.

### Step 3:
Copy and change the USERNAME according to your username.

### Step 4:
Run it and wait a couple of seconds.

### Step 5:
You can copy and paste your data. Type 'copy(followers)' or 'copy(followings)' or 'copy(dontFollowMeBack)' or 'copy(iDontFollowBack)' in the console and paste to a text editor to see your data.

Enjoy 🚀!



```
const username = "USERNAME";

let followers = [];
let followings = [];
let dontFollowMeBack = [];
let iDontFollowBack = [];

(async () => {
  try {
    console.log("Process started! Give it a couple of seconds");
    const userQueryRes = await fetch(
      `https://www.instagram.com/web/search/topsearch/?query=${username}`
    );

    const userQueryJson = await userQueryRes.json();

    const userId = userQueryJson.users
      .map((u) => u.user)
      .find((u) => u.username === username)?.pk;

    if (!userId) {
      throw new Error("User not found!");
    }

    let after = null;
    let has_next = true;

    while (has_next) {
      const res = await fetch(
        `https://www.instagram.com/graphql/query/?query_hash=c76146de99bb02f6415203be841dd25a&variables=${encodeURIComponent(
          JSON.stringify({
            id: userId,
            include_reel: true,
            fetch_mutual: true,
            first: 50,
            after: after,
          })
        )}`
      ).then((res) => res.json());

      const pageInfo = res.data?.user?.edge_followed_by?.page_info;
      if (!pageInfo) throw new Error("Unexpected API response structure");

      has_next = pageInfo.has_next_page;
      after = pageInfo.end_cursor;
      followers = followers.concat(
        res.data.user.edge_followed_by.edges.map(({ node }) => ({
          username: node.username,
          full_name: node.full_name,
        }))
      );
    }

    console.log({ followers });

    after = null;
    has_next = true;

    while (has_next) {
      const res = await fetch(
        `https://www.instagram.com/graphql/query/?query_hash=d04b0a864b4b54837c0d870b0e77e076&variables=${encodeURIComponent(
          JSON.stringify({
            id: userId,
            include_reel: true,
            fetch_mutual: true,
            first: 50,
            after: after,
          })
        )}`
      ).then((res) => res.json());

      const pageInfo = res.data?.user?.edge_follow?.page_info;
      if (!pageInfo) throw new Error("Unexpected API response structure");

      has_next = pageInfo.has_next_page;
      after = pageInfo.end_cursor;
      followings = followings.concat(
        res.data.user.edge_follow.edges.map(({ node }) => ({
          username: node.username,
          full_name: node.full_name,
        }))
      );
    }

    console.log({ followings });

    dontFollowMeBack = followings.filter(
      (following) =>
        !followers.find((follower) => follower.username === following.username)
    );

    console.log({ dontFollowMeBack });

    iDontFollowBack = followers.filter(
      (follower) =>
        !followings.find((following) => following.username === follower.username)
    );

    console.log({ iDontFollowBack });

    console.log(
      "Process is done: Type 'copy(followers)' or 'copy(followings)' or 'copy(dontFollowMeBack)' or 'copy(iDontFollowBack)' in the console and paste it into a text editor to take a look at it"
    );
  } catch (err) {
    console.log({ err });
  }
})();
```
