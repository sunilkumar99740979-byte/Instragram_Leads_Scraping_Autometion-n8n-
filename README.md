{
  "nodes": [
    {
      "parameters": {
        "formTitle": "Instragram Scraper",
        "formDescription": "Generate Leads From Apify",
        "formFields": {
          "values": [
            {
              "fieldLabel": "Location"
            },
            {
              "fieldLabel": "Hairstage"
            },
            {
              "fieldLabel": "bio"
            },
            {
              "fieldLabel": "geo"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.formTrigger",
      "typeVersion": 2.5,
      "position": [
        -4416,
        -912
      ],
      "id": "a249b184-d5c6-48a8-9453-d6acd33df440",
      "name": "On form submission",
      "webhookId": "fe76eb10-7de9-4789-86a3-978f9edba85a"
    },
    {
      "parameters": {
        "method": "POST",
        "url": "=https://api.apify.com/v2/acts/apify~instagram-scraper/run-sync-get-dataset-items?token=apify_api_",
        "sendBody": true,
        "specifyBody": "json",
        "jsonBody": "={\n    \"addParentData\": false,\n    \"onlyPostsNewerThan\": \"1 month\",\n    \"resultsLimit\": 1,\n    \"resultsType\": \"details\",\n    \"search\": \" {{ $json.Hairstage }}\",\n    \"searchLimit\": 1,\n    \"searchType\": \"user\"\n}",
        "options": {}
      },
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.4,
      "position": [
        -4000,
        -912
      ],
      "id": "b7fb0006-524d-4a21-9148-2369cd5c3bab",
      "name": "HTTP Request"
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "9e88fcf8-58af-4436-978a-0284ca160fd0",
              "name": "Hairstage",
              "value": "={{ $json.Hairstage }} in {{ $json.Location }}",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        -4208,
        -912
      ],
      "id": "5a8c675f-3bcb-412b-9d71-eb4cb1655b28",
      "name": "Edit Fields"
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.wait",
      "typeVersion": 1.1,
      "position": [
        -3792,
        -912
      ],
      "id": "41d34eff-f895-40a4-94ec-75240f5e7f55",
      "name": "Wait",
      "webhookId": "d902a863-86cc-4c8d-b751-10620863b7cf"
    },
    {
      "parameters": {
        "jsCode": "// Apify Instagram Data ko normalize karne ke liye\nconst items = $input.all();\nconst normalizedLeads = [];\n\nfor (const item of items) {\n    const raw = item.json;\n    \n    // Assignment 01 ke liye zaroori fields nikalna\n    normalizedLeads.push({\n        json: {\n            id: raw.id || \"N/A\",\n            username: raw.username || \"N/A\",\n            fullName: raw.fullName || \"N/A\",\n            bio: raw.biography || \"No Bio\",\n            followers: raw.followersCount || 0,\n            following: raw.followsCount || 0,\n            postsCount: raw.postsCount || 0,\n            profileUrl: raw.url || `https://www.instagram.com/${raw.username}/`,\n            website: raw.externalUrl || \"N/A\",\n            category: raw.businessCategoryName || \"N/A\",\n            // Location agar available ho (Surat/Gujarat leads filter karne ke liye)\n            locationName: raw.latestPosts && raw.latestPosts[0]?.locationName ? raw.latestPosts[0].locationName : \"Surat, Gujarat\",\n            // Quality Filter Logic: Kya ye lead kaam ki hai?\n            isHighQuality: (raw.followersCount > 100) ? \"Yes\" : \"No\",\n            // Last active check\n            lastActive: raw.latestPosts && raw.latestPosts[0] ? raw.latestPosts[0].timestamp : \"N/A\"\n        }\n    });\n}\n\nreturn normalizedLeads;"
      },
      "id": "81747670-ecbb-49c2-9574-ad7cd2bcbb90",
      "name": "Normalize Data",
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [
        -3536,
        -912
      ]
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "8b3996f2-bfb7-49d3-b072-115e7d09520a",
              "name": "User Name",
              "value": "={{ $json.username }}",
              "type": "string"
            },
            {
              "id": "65c2b581-57a4-4ce6-ad07-f6fa46dde82e",
              "name": "Full Name",
              "value": "={{ $json.fullName }}",
              "type": "string"
            },
            {
              "id": "d7d0dff1-319f-4874-9f80-868a31e07ee5",
              "name": "Bio",
              "value": "={{ $json.bio }}",
              "type": "string"
            },
            {
              "id": "44439dc2-dc41-4933-89f6-f21c11756865",
              "name": "Followers",
              "value": "={{ $json.followers }}",
              "type": "number"
            },
            {
              "id": "b895c5f8-91f3-440d-9a53-21791f43f961",
              "name": "Following",
              "value": "={{ $json.following }}",
              "type": "number"
            },
            {
              "id": "14105119-b3c3-41e2-9895-13480fc343d5",
              "name": "Post Count",
              "value": "={{ $json.postsCount }}",
              "type": "number"
            },
            {
              "id": "14a25079-3c1d-4e34-8c16-c80adbfcf77a",
              "name": "URL",
              "value": "={{ $json.profileUrl }}",
              "type": "string"
            },
            {
              "id": "85bbebfe-0760-49a4-b4ab-0a1a65d45627",
              "name": "Website",
              "value": "={{ $json.website }}",
              "type": "string"
            },
            {
              "id": "b9f8e695-1f50-47c2-9292-d2ac08caed4a",
              "name": "Location",
              "value": "={{ $json.locationName }}",
              "type": "string"
            },
            {
              "id": "1cfb5aa2-37a2-49aa-b3f5-c9c2e9330a4e",
              "name": "High Quality",
              "value": "={{ $json.isHighQuality }}",
              "type": "string"
            },
            {
              "id": "92049fde-1db2-440e-b0ab-dbb691f3dd9f",
              "name": "Active Status",
              "value": "={{ $json.lastActive }}",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        -3328,
        -912
      ],
      "id": "bb037210-5d2c-4006-8da4-fc80f5574c59",
      "name": "Edit Fields1"
    },
    {
      "parameters": {
        "operation": "append",
        "documentId": {
          "__rl": true,
          "value": "1HxKi3DSBsu-D5mxWws4twYSyHdWwGGBu5TzqXoPW_ME",
          "mode": "list",
          "cachedResultName": "indeed Project ",
          "cachedResultUrl": "https://docs.google.com/spreadsheets/d/1HxKi3DSBsu-D5mxWws4twYSyHdWwGGBu5TzqXoPW_ME/edit?usp=drivesdk"
        },
        "sheetName": {
          "__rl": true,
          "value": "gid=0",
          "mode": "list",
          "cachedResultName": "Instragram Ledd From Apify",
          "cachedResultUrl": "https://docs.google.com/spreadsheets/d/1HxKi3DSBsu-D5mxWws4twYSyHdWwGGBu5TzqXoPW_ME/edit#gid=0"
        },
        "columns": {
          "mappingMode": "defineBelow",
          "value": {
            "User Name": "={{ $json['User Name'] }}",
            "Full Name": "={{ $json['Full Name'] }}",
            "Bio": "={{ $json.Bio }}",
            "Followers": "={{ $json.Followers }}",
            "Post Count": "={{ $json['Post Count'] }}",
            "Following": "={{ $json.Following }}",
            "URL": "={{ $json.URL }}",
            "Website": "={{ $json.Website }}",
            "Location": "={{ $json.Location }}",
            "High Quality": "={{ $json['High Quality'] }}",
            "Active Status": "={{ $json['Active Status'] }}"
          },
          "matchingColumns": [],
          "schema": [
            {
              "id": "User Name",
              "displayName": "User Name",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "Full Name",
              "displayName": "Full Name",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "Bio",
              "displayName": "Bio",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "Followers",
              "displayName": "Followers",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "Following",
              "displayName": "Following",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "Post Count",
              "displayName": "Post Count",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "URL",
              "displayName": "URL",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "Website",
              "displayName": "Website",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "Location",
              "displayName": "Location",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "High Quality",
              "displayName": "High Quality",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            },
            {
              "id": "Active Status",
              "displayName": "Active Status",
              "required": false,
              "defaultMatch": false,
              "display": true,
              "type": "string",
              "canBeUsedToMatch": true
            }
          ],
          "attemptToConvertTypes": false,
          "convertFieldsToString": false
        },
        "options": {}
      },
      "type": "n8n-nodes-base.googleSheets",
      "typeVersion": 4.7,
      "position": [
        -3136,
        -912
      ],
      "id": "f176f13a-1e26-47c7-96b5-9b5ea6f33e07",
      "name": "Save Leads in Instragram Leads",
      "credentials": {
        "googleSheetsOAuth2Api": {
          "id": "1KeqeQ7zYUbUyZBX",
          "name": "Google Sheets account"
        }
      }
    }
  ],
  "connections": {
    "On form submission": {
      "main": [
        [
          {
            "node": "Edit Fields",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "HTTP Request": {
      "main": [
        [
          {
            "node": "Wait",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Edit Fields": {
      "main": [
        [
          {
            "node": "HTTP Request",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Wait": {
      "main": [
        [
          {
            "node": "Normalize Data",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Normalize Data": {
      "main": [
        [
          {
            "node": "Edit Fields1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Edit Fields1": {
      "main": [
        [
          {
            "node": "Save Leads in Instragram Leads",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Save Leads in Instragram Leads": {
      "main": [
        []
      ]
    }
  },
  "pinData": {
    "On form submission": [
      {
        "Location": "Surat",
        "Hairstage": "Founders",
        "bio": "",
        "geo": "",
        "submittedAt": "2026-03-18T17:37:34.546+05:30",
        "formMode": "test"
      }
    ],
    "HTTP Request": [
      {
        "searchTerm": "Founders in Surat",
        "searchSource": "google",
        "inputUrl": "https://www.instagram.com/frcsurat/",
        "id": "77819791713",
        "username": "frcsurat",
        "url": "https://www.instagram.com/frcsurat",
        "fullName": "FRC Surat",
        "biography": "Surat 🇮🇳 chapter of @foundersrc\nSocial miles for builders, operators and investors\nBy @shaleen_poddar @_archiii.__ and @agarwal_puru",
        "externalUrls": [
          {
            "title": "",
            "lynx_url": "https://l.instagram.com/?u=http%3A%2F%2Ffoundersrc.com%2F&e=AT5EXvZzBzShuqCzx-gOmpuFXz0XY6cIBNkrIXtFen3nVxL-y82DgM-u85ySi81xsWU1m4fiLWVY-7an_6H69pnzMXvsvtjk",
            "url": "http://foundersrc.com",
            "link_type": "external"
          }
        ],
        "externalUrl": "http://foundersrc.com/",
        "externalUrlShimmed": "https://l.instagram.com/?u=http%3A%2F%2Ffoundersrc.com%2F&e=AT4jMmqKTJq3MPCKEh2dqbBLd_Q2GBY3ECMw_39IwX3A-8NH6dLkkdMM-PWr8sj0AsAxH-5facbBZ0lrfaXv1A0eQSE6uIIq",
        "followersCount": 806,
        "followsCount": 4,
        "hasChannel": false,
        "highlightReelCount": 16,
        "isBusinessAccount": false,
        "joinedRecently": false,
        "businessCategoryName": "Community",
        "private": false,
        "verified": false,
        "profilePicUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-19/571265983_17843245041599714_7567859433107320002_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby41NTUuYzIifQ&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=2SdPT5Ay0VEQ7kNvwFb6Y9U&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyzKSn4DjqmL64qiZEW9SsGU2U-7Mv1mQHtk9dc3KDnVA&oe=69C0726C&_nc_sid=8b3546",
        "profilePicUrlHD": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-19/571265983_17843245041599714_7567859433107320002_n.jpg?stp=dst-jpg_s320x320_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby41NTUuYzIifQ&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=2SdPT5Ay0VEQ7kNvwFb6Y9U&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfwpqHfkg4-qQ0gowmE18_HbCz8rFsB94WVIMkj-ynvUsg&oe=69C0726C&_nc_sid=8b3546",
        "igtvVideoCount": 0,
        "relatedProfiles": [],
        "latestIgtvVideos": [],
        "postsCount": 33,
        "latestPosts": [
          {
            "id": "3852678278058634725",
            "type": "Video",
            "shortCode": "DV3dntFjG3l",
            "caption": "Now it’s a Saturday ritual with FRCsurat 🙂‍↔️\n\n#foundersrunningclub #frcsurat #runningcommunity #founders #explorepage",
            "hashtags": [
              "foundersrunningclub",
              "frcsurat",
              "runningcommunity",
              "founders",
              "explorepage"
            ],
            "mentions": [],
            "url": "https://www.instagram.com/p/DV3dntFjG3l/",
            "commentsCount": 10,
            "dimensionsHeight": 1920,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/652827635_17865115482599714_6375829825236342026_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=_VH3lnzkV4AQ7kNvwFJWS05&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfwSVXwyLpbGDkuUQTsEq2_rBkoNsrxItvXeDvMMYcNhRw&oe=69C05F2B&_nc_sid=8b3546",
            "images": [],
            "videoUrl": "https://scontent-ord5-1.cdninstagram.com/o1/v/t16/f2/m69/AQOM3vS7rbVE1MkxqM8e6IitO9A6yWGop0JYHw61Mm2_jKkcFvnAyAntlXba6MZMaW62IXrQQMLGiZzC3NIidtSu.mp4?strext=1&_nc_cat=111&_nc_sid=5e9851&_nc_ht=scontent-ord5-1.cdninstagram.com&_nc_ohc=Wtgcqgfeu24Q7kNvwGFjNb1&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0xJUFMuQzMuNzIwLmRhc2hfYmFzZWxpbmVfMV92MSIsInhwdl9hc3NldF9pZCI6MTc4NjUxMTUxNTU1OTk3MTQsImFzc2V0X2FnZV9kYXlzIjozLCJ2aV91c2VjYXNlX2lkIjoxMDA5OSwiZHVyYXRpb25fcyI6OCwidXJsZ2VuX3NvdXJjZSI6Ind3dyJ9&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=92a643f346136b7f&_nc_vs=HBksFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HTXFhbWlhd1JIUjlzdjBFQUdtVTBlNnA0a3BaYnNwVEFRQUYVAALIARIAFQIYUWlnX3hwdl9wbGFjZW1lbnRfcGVybWFuZW50X3YyLzc0NDBCMzc2MzE0NThFQjNCQjMwNDM3MDQ1RDdFMTkwX2F1ZGlvX2Rhc2hpbml0Lm1wNBUCAsgBEgAoABgAGwKIB3VzZV9vaWwBMRJwcm9ncmVzc2l2ZV9yZWNpcGUBMRUAACbExcSryI68PxUCKAJDMywXQCELxqfvnbIYEmRhc2hfYmFzZWxpbmVfMV92MREAdf4HZeadAQA&oh=00_AfwVfH-HeFlkghwin9ALHtab0lD6v44rvp6gvk5_EYU0ww&oe=69C06D25",
            "alt": null,
            "likesCount": 182,
            "videoViewCount": 4443,
            "timestamp": "2026-03-14T13:34:16.000Z",
            "childPosts": [],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "productType": "clips",
            "taggedUsers": [
              {
                "full_name": "Archi Jain",
                "id": "5789571140",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-1.cdninstagram.com/v/t51.82787-19/610189149_18389838961195141_1688913760359741680_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-1.cdninstagram.com&_nc_cat=101&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=qUPw_aebsi8Q7kNvwEVuHYS&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzFBuXoL1UU8-lsdcQC7wbp-S9ZweAyqF7lKNZZ4YhEYg&oe=69C04FD2&_nc_sid=8b3546",
                "username": "_archiii.__"
              },
              {
                "full_name": "Shaleen Poddar",
                "id": "9130508435",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.2885-19/500442939_18195571684308436_2489791778997007579_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=104&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=eWOW5v42ISkQ7kNvwHCp68b&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyXh7fC7IAJr57jTFT6sJcWW7JHVYG1MJ7JlN1RH8D37Q&oe=69C083F2&_nc_sid=8b3546",
                "username": "shaleen_poddar"
              },
              {
                "full_name": "Puru Agarwal",
                "id": "2201900786",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.2885-19/300242437_174699415052802_3501114932006103194_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4zOTcuYzIifQ&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=106&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=cJ2_LIjG-VYQ7kNvwEQvJOe&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afwb_M19BRs6zGJUVh3RmtTR9AClRCh82r4M_9lAWXdD6Q&oe=69C07DB5&_nc_sid=8b3546",
                "username": "agarwal_puru"
              },
              {
                "full_name": "Three O’ Clock Surat",
                "id": "79744654356",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-19/609226958_17846812431662357_2741166277297017979_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=105&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=BKQQfyk_zTcQ7kNvwHUahuX&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyFuEfQnJRAGLVfeqqEySxhi7Ig5jw51DUKeND2xQCNkw&oe=69C059B9&_nc_sid=8b3546",
                "username": "threeoclock.stv"
              }
            ],
            "musicInfo": {
              "artist_name": "Roger Sanchez, HUGEL",
              "song_name": "Go Again",
              "uses_original_audio": false,
              "should_mute_audio": false,
              "should_mute_audio_reason": "",
              "audio_id": "1423283185045801"
            },
            "isCommentsDisabled": false
          },
          {
            "id": "3851228709852546233",
            "type": "Image",
            "shortCode": "DVyUBtfjAS5",
            "caption": "The hardest part isn’t the miles; it’s deciding to show up when the rest of the world is still sleeping. \nSaturday morning is your chance to prove to yourself that your goals mean more than your excuses.\n\nSee you at the start line.\n• 📍 Location: @threeoclock.stv \n• ⏰ Time: 6:30 AM\n• 🗓️ Date: Saturday, 14/03",
            "hashtags": [],
            "mentions": [
              "threeoclock.stv"
            ],
            "url": "https://www.instagram.com/p/DVyUBtfjAS5/",
            "commentsCount": 0,
            "dimensionsHeight": 1276,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/649820753_17864759664599714_4527243816058492638_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=5Svcyxv76YEQ7kNvwF4RpQV&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&ig_cache_key=Mzg1MTIyODcwOTg1MjU0NjIzMw%3D%3D.3-ccb7-5&oh=00_AfyKXzFJ7vbrzxQtHp7bNlxpTrdY6s-EIVpwNs6SF4_AIQ&oe=69C07B62&_nc_sid=8b3546",
            "images": [],
            "alt": "Photo shared by FRC Surat on March 12, 2026 tagging @threeoclock.stv. May be an image of one or more people, digital watch, television, poster, portable cassette player, timer and text that says 'CSTV C STV 14-03-2026 YOUvs.YOU YOU νл. YOU 6:15 Am sharp THREE THREEO'CLOCK O'CLOCK'.",
            "likesCount": 66,
            "timestamp": "2026-03-12T13:32:58.000Z",
            "childPosts": [],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "taggedUsers": [
              {
                "full_name": "Three O’ Clock Surat",
                "id": "79744654356",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-19/609226958_17846812431662357_2741166277297017979_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=105&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=BKQQfyk_zTcQ7kNvwHUahuX&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyFuEfQnJRAGLVfeqqEySxhi7Ig5jw51DUKeND2xQCNkw&oe=69C059B9&_nc_sid=8b3546",
                "username": "threeoclock.stv"
              }
            ],
            "isCommentsDisabled": false
          },
          {
            "id": "3848031722550347434",
            "type": "Video",
            "shortCode": "DVm9HbDDEaq",
            "caption": "Happy Women’s Day to all the incredible women of FRC! Thank you for the technical brilliance, leadership, and passion you bring to this team. You make us better every day! \n\n#foundersrunningclub #frcsurat #runningcommunity #founders",
            "hashtags": [
              "foundersrunningclub",
              "frcsurat",
              "runningcommunity",
              "founders"
            ],
            "mentions": [],
            "url": "https://www.instagram.com/p/DVm9HbDDEaq/",
            "commentsCount": 4,
            "dimensionsHeight": 1920,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/649080938_17863854291599714_9171553350141813338_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=5VjmFhM8kJYQ7kNvwHJIGyU&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afz8-zx90VH6MH7qfeEKvmUvHJ4NDDI4KaWYGPt2urVTbQ&oe=69C04D9F&_nc_sid=8b3546",
            "images": [],
            "videoUrl": "https://scontent-ord5-2.cdninstagram.com/o1/v/t16/f2/m69/AQO-DKGdDT21nmIHHjc5UtQbXUiq0WdQpmYrWW1bJxep7-1G1BPIZSwE9ywv981BdbODtrrKdnOsmTMwtUsqnp0F.mp4?strext=1&_nc_cat=102&_nc_sid=5e9851&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_ohc=dQvSwq0NhakQ7kNvwF_vZ09&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0xJUFMuQzMuNzIwLmRhc2hfYmFzZWxpbmVfMV92MSIsInhwdl9hc3NldF9pZCI6MTc4NjM4NTM2MzQ1OTk3MTQsImFzc2V0X2FnZV9kYXlzIjoxMCwidmlfdXNlY2FzZV9pZCI6MTAwOTksImR1cmF0aW9uX3MiOjE2LCJ1cmxnZW5fc291cmNlIjoid3d3In0%3D&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=60c3a0bba81d219b&_nc_vs=HBksFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HQkxybGlaNzVkd3ljWk1GQVBoU3pGNWJLMUZuYnNwVEFRQUYVAALIARIAFQIYUWlnX3hwdl9wbGFjZW1lbnRfcGVybWFuZW50X3YyLzQyNEY4Q0U0MDA0MjVFRTBEMUQ2RDUwM0RFQjRENjgxX2F1ZGlvX2Rhc2hpbml0Lm1wNBUCAsgBEgAoABgAGwKIB3VzZV9vaWwBMRJwcm9ncmVzc2l2ZV9yZWNpcGUBMRUAACbE7K2jkcW7PxUCKAJDMywXQDDEGJN0vGoYEmRhc2hfYmFzZWxpbmVfMV92MREAdf4HZeadAQA&oh=00_AfzSYFO9bwMpnZi61gGqgStY5e0NlOC8XzIrOtS1z5kEOg&oe=69C05102",
            "alt": null,
            "likesCount": 157,
            "videoViewCount": 3359,
            "timestamp": "2026-03-08T03:44:06.000Z",
            "childPosts": [],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "productType": "clips",
            "taggedUsers": [
              {
                "full_name": "Archi Jain",
                "id": "5789571140",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-1.cdninstagram.com/v/t51.82787-19/610189149_18389838961195141_1688913760359741680_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-1.cdninstagram.com&_nc_cat=101&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=qUPw_aebsi8Q7kNvwEVuHYS&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzFBuXoL1UU8-lsdcQC7wbp-S9ZweAyqF7lKNZZ4YhEYg&oe=69C04FD2&_nc_sid=8b3546",
                "username": "_archiii.__"
              }
            ],
            "musicInfo": {
              "artist_name": "Maroon 5",
              "song_name": "Girls Like You",
              "uses_original_audio": false,
              "should_mute_audio": false,
              "should_mute_audio_reason": "",
              "audio_id": "2099520520337998"
            },
            "isCommentsDisabled": false
          },
          {
            "id": "3846563578115809899",
            "type": "Sidecar",
            "shortCode": "DVhvTG_jGJr",
            "caption": "We are taking over Pakiki for a special 5km/3km dedicated to the women who move this club forward.\n\nDon’t come alone! This is the perfect Saturday to grab your sister, your best friend, or that colleague who’s been saying “I should start running.” Let’s make them feel the FRC magic! There is a specific kind of energy that happens when we move together, an empowerment you simply can’t find anywhere else.\n\nDon’t be the one watching the highlights on the story later; be in the middle of the pack with your favorite person by your side.\n\nThe Details:\n📍 Location: @pakikisurat (Piplod)\n📅 When: This Saturday, 07/03/2026\n⏰ Time: 6:30AM\n👟 Distance: 5km/3km",
            "hashtags": [],
            "mentions": [
              "pakikisurat"
            ],
            "url": "https://www.instagram.com/p/DVhvTG_jGJr/",
            "commentsCount": 0,
            "dimensionsHeight": 1191,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/645954912_17863482855599714_4640872053504582196_n.jpg?stp=dst-jpg_e15_fr_p1080x1080_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=BMUkJkV_VBMQ7kNvwE50RlI&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&ig_cache_key=Mzg0NjU2MzQ3MDA5NTcwODA0Mg%3D%3D.3-ccb7-5&oh=00_AfwT1Rv3YOUILQcggwgOyPolvnL6yGWz4VFXf0vg9T4Q2w&oe=69C05FA5&_nc_sid=8b3546",
            "images": [
              "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/645954912_17863482855599714_4640872053504582196_n.jpg?stp=dst-jpg_e15_fr_p1080x1080_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=BMUkJkV_VBMQ7kNvwE50RlI&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&ig_cache_key=Mzg0NjU2MzQ3MDA5NTcwODA0Mg%3D%3D.3-ccb7-5&oh=00_AfwT1Rv3YOUILQcggwgOyPolvnL6yGWz4VFXf0vg9T4Q2w&oe=69C05FA5&_nc_sid=8b3546",
              "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/645987907_17863482927599714_6546801523357848580_n.jpg?stp=dst-jpg_e15_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=TX0cw_i4GZ8Q7kNvwEbGhYq&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfxhFyd5y0mFqEgnZI-pTgDwJHx1wc2GfxVUJfEWT1L1OA&oe=69C07FB9&_nc_sid=8b3546",
              "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/645944599_17863482930599714_7095236587021525570_n.jpg?stp=dst-jpg_e15_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=XcAQpSKjK7wQ7kNvwFzqvYv&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzucxvtbAuEZuJ6nkehuZHmbsqZrNTTqA4-3PnuMnboiA&oe=69C05150&_nc_sid=8b3546",
              "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/648568697_17863482957599714_241413389572050696_n.jpg?stp=dst-jpg_e15_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=_0xzhe8B1bgQ7kNvwE_MLe3&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfwdkWQGM4wWixiwCV8CIjOnKuF6dVjCWrZkieZhTp_Lrg&oe=69C0706A&_nc_sid=8b3546"
            ],
            "alt": "Photo by FRC Surat on March 05, 2026. May be an image of one or more people, people playing tennis, poster, racket and text that says 'CSTV C STV 07-03-2026 2026 She runs, she leads, she conquers Join us at Pakiki as we celebrate Women's Day on the move. 6:30'.",
            "likesCount": 75,
            "timestamp": "2026-03-06T03:02:13.000Z",
            "childPosts": [
              {
                "id": "3846563470095708042",
                "type": "Image",
                "shortCode": "DVhvRiZDG-K",
                "caption": "",
                "hashtags": [],
                "mentions": [],
                "url": "https://www.instagram.com/p/DVhvRiZDG-K/",
                "commentsCount": 0,
                "firstComment": "",
                "latestComments": [],
                "dimensionsHeight": 1191,
                "dimensionsWidth": 1080,
                "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/645954912_17863482855599714_4640872053504582196_n.jpg?stp=dst-jpg_e15_fr_p1080x1080_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=BMUkJkV_VBMQ7kNvwE50RlI&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&ig_cache_key=Mzg0NjU2MzQ3MDA5NTcwODA0Mg%3D%3D.3-ccb7-5&oh=00_AfwT1Rv3YOUILQcggwgOyPolvnL6yGWz4VFXf0vg9T4Q2w&oe=69C05FA5&_nc_sid=8b3546",
                "images": [],
                "alt": "Photo by FRC Surat on March 05, 2026. May be an image of one or more people, people playing tennis, poster, racket and text that says 'CSTV C STV 07-03-2026 2026 She runs, she leads, she conquers Join us at Pakiki as we celebrate Women's Day on the move. 6:30'.",
                "likesCount": null,
                "timestamp": null,
                "childPosts": [],
                "ownerUsername": "frcsurat",
                "ownerId": "77819791713"
              },
              {
                "id": "3846563476294874325",
                "type": "Video",
                "shortCode": "DVhvRoKjDTV",
                "caption": "",
                "hashtags": [],
                "mentions": [],
                "url": "https://www.instagram.com/p/DVhvRoKjDTV/",
                "commentsCount": 0,
                "firstComment": "",
                "latestComments": [],
                "dimensionsHeight": 827,
                "dimensionsWidth": 750,
                "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/645987907_17863482927599714_6546801523357848580_n.jpg?stp=dst-jpg_e15_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=TX0cw_i4GZ8Q7kNvwEbGhYq&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfxhFyd5y0mFqEgnZI-pTgDwJHx1wc2GfxVUJfEWT1L1OA&oe=69C07FB9&_nc_sid=8b3546",
                "images": [],
                "videoUrl": "https://scontent-ord5-2.cdninstagram.com/o1/v/t16/f2/m69/AQMeqcdEbqQagXJp85vzp5o1SSTuGqE0u75zgDamJWZG6ZfMOJh-HoZVVauUp2fOTpzA6JET378HUlQdMwEBiGud.mp4?strext=1&_nc_cat=105&_nc_sid=5e9851&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_ohc=P6NYykK4ZJkQ7kNvwHLK6l_&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0FST1VTRUxfSVRFTS5DMy43MTYuZGFzaF9iYXNlbGluZV8xX3YxIiwieHB2X2Fzc2V0X2lkIjoxNzg2MzQ4Mjg2MTU5OTcxNCwiYXNzZXRfYWdlX2RheXMiOjEyLCJ2aV91c2VjYXNlX2lkIjoxMDE0NiwiZHVyYXRpb25fcyI6MiwidXJsZ2VuX3NvdXJjZSI6Ind3dyJ9&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=95bf41d177027907&_nc_vs=HBkcFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HSzRLa1NidWJ1R3Q4SzhGQUlOeDVFQkNuc05ZYnNwVEFRQUYVAALIARIAKAAYABsCiAd1c2Vfb2lsATEScHJvZ3Jlc3NpdmVfcmVjaXBlATEVAAAmxO_V5savuz8VAigCQzMsF0AIAAAAAAAAGBJkYXNoX2Jhc2VsaW5lXzFfdjERAHXuB2XEngEA&oh=00_AfySNfl4S5LRhwxGbUOhUfMCGt7_pVOkUJECJ_lueDIOKA&oe=69C0636F",
                "alt": null,
                "likesCount": null,
                "videoViewCount": 379,
                "timestamp": null,
                "childPosts": [],
                "ownerUsername": "frcsurat",
                "ownerId": "77819791713"
              },
              {
                "id": "3846563492686218806",
                "type": "Video",
                "shortCode": "DVhvR3bjEY2",
                "caption": "",
                "hashtags": [],
                "mentions": [],
                "url": "https://www.instagram.com/p/DVhvR3bjEY2/",
                "commentsCount": 0,
                "firstComment": "",
                "latestComments": [],
                "dimensionsHeight": 827,
                "dimensionsWidth": 750,
                "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/645944599_17863482930599714_7095236587021525570_n.jpg?stp=dst-jpg_e15_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=XcAQpSKjK7wQ7kNvwFzqvYv&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzucxvtbAuEZuJ6nkehuZHmbsqZrNTTqA4-3PnuMnboiA&oe=69C05150&_nc_sid=8b3546",
                "images": [],
                "videoUrl": "https://scontent-ord5-2.cdninstagram.com/o1/v/t16/f2/m69/AQP9kGiaQI0HBeGGNnr5wOGU8rzMNyk7p5bdhDCn1Z7wFQYG4zflme3jn7bzv4id7zDJeZyPTs7D6SWnNpfPaoFm.mp4?strext=1&_nc_cat=110&_nc_sid=5e9851&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_ohc=UMaDp-G208IQ7kNvwFXMyIB&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0FST1VTRUxfSVRFTS5DMy43MTYuZGFzaF9iYXNlbGluZV8xX3YxIiwieHB2X2Fzc2V0X2lkIjoxNzg2MzQ4Mjg3NjU5OTcxNCwiYXNzZXRfYWdlX2RheXMiOjEyLCJ2aV91c2VjYXNlX2lkIjoxMDE0NiwiZHVyYXRpb25fcyI6MCwidXJsZ2VuX3NvdXJjZSI6Ind3dyJ9&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=8673d40ce5c9ce9c&_nc_vs=HBkcFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HSFlZaGlaRnhoQm12elFEQUw4S0VqRXBmZzgzYnNwVEFRQUYVAALIARIAKAAYABsCiAd1c2Vfb2lsATEScHJvZ3Jlc3NpdmVfcmVjaXBlATEVAAAmxPb89Mavuz8VAigCQzMsFz_ndLxqfvnbGBJkYXNoX2Jhc2VsaW5lXzFfdjERAHXuB2XEngEA&oh=00_Afxm73H8kriyDUX5ffyl0Yfew25L548UfWGNG8Ptp1Hu8Q&oe=69C071B2",
                "alt": null,
                "likesCount": null,
                "videoViewCount": 450,
                "timestamp": null,
                "childPosts": [],
                "ownerUsername": "frcsurat",
                "ownerId": "77819791713"
              },
              {
                "id": "3846563495152469898",
                "type": "Video",
                "shortCode": "DVhvR5ujEeK",
                "caption": "",
                "hashtags": [],
                "mentions": [],
                "url": "https://www.instagram.com/p/DVhvR5ujEeK/",
                "commentsCount": 0,
                "firstComment": "",
                "latestComments": [],
                "dimensionsHeight": 827,
                "dimensionsWidth": 750,
                "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/648568697_17863482957599714_241413389572050696_n.jpg?stp=dst-jpg_e15_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=_0xzhe8B1bgQ7kNvwE_MLe3&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfwdkWQGM4wWixiwCV8CIjOnKuF6dVjCWrZkieZhTp_Lrg&oe=69C0706A&_nc_sid=8b3546",
                "images": [],
                "videoUrl": "https://scontent-ord5-3.cdninstagram.com/o1/v/t16/f2/m69/AQP2XZ2X5IFGpCaL1sNZw4UZfcwPMxPCDB6Rv4CxIvZu30p9VhnQZIcMzapRoNaLwi9YmVBUSlkwhtSBBnTQuMud.mp4?strext=1&_nc_cat=109&_nc_sid=5e9851&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_ohc=93ZOIO7S8e0Q7kNvwG24GNg&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0FST1VTRUxfSVRFTS5DMy43MTYuZGFzaF9iYXNlbGluZV8xX3YxIiwieHB2X2Fzc2V0X2lkIjoxNzg2MzQ4Mjg5NDU5OTcxNCwiYXNzZXRfYWdlX2RheXMiOjEyLCJ2aV91c2VjYXNlX2lkIjoxMDE0NiwiZHVyYXRpb25fcyI6MSwidXJsZ2VuX3NvdXJjZSI6Ind3dyJ9&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=a9b3054544b4a022&_nc_vs=HBkcFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HRWE2cGlicmxpZTlYRkFFQUFPY0VpLWhiOHRsYnNwVEFRQUYVAALIARIAKAAYABsCiAd1c2Vfb2lsATEScHJvZ3Jlc3NpdmVfcmVjaXBlATEVAAAmxJiShsevuz8VAigCQzMsFz_4AAAAAAAAGBJkYXNoX2Jhc2VsaW5lXzFfdjERAHXuB2XEngEA&oh=00_AfxyzvQtB6WhPwn4Bbg4gtUU6meqvXeQVnBFc-PRF0RPpw&oe=69C07A7F",
                "alt": null,
                "likesCount": null,
                "videoViewCount": 349,
                "timestamp": null,
                "childPosts": [],
                "ownerUsername": "frcsurat",
                "ownerId": "77819791713"
              }
            ],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "isCommentsDisabled": false
          },
          {
            "id": "3845115110797136132",
            "type": "Video",
            "shortCode": "DVcl9IrjNEE",
            "caption": "When you celebrate holi with your running crew \n\n#foundersrunningclub #holi #running",
            "hashtags": [
              "foundersrunningclub",
              "holi",
              "running"
            ],
            "mentions": [],
            "url": "https://www.instagram.com/p/DVcl9IrjNEE/",
            "commentsCount": 17,
            "dimensionsHeight": 1920,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/645793404_17863116279599714_2145518707477068625_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=e6vZA7I-5kcQ7kNvwGcx3Tr&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyqdJv_aFtLi-jW8EpsXrwpvbDMSwXn6mXVG-4oiLxdlA&oe=69C0602E&_nc_sid=8b3546",
            "images": [],
            "videoUrl": "https://scontent-ord5-1.cdninstagram.com/o1/v/t16/f2/m69/AQNrFRCCIDzJ71FrfNZvTCVKUH-qqg6fYrJ9hbr1tAsxck0lSVKZTHgkE9UZm2feYyykAhTUPsVfej1203nqormS.mp4?strext=1&_nc_cat=108&_nc_sid=5e9851&_nc_ht=scontent-ord5-1.cdninstagram.com&_nc_ohc=_0YX_60j_rUQ7kNvwEspzpm&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0xJUFMuQzMuNzIwLmRhc2hfYmFzZWxpbmVfMV92MSIsInhwdl9hc3NldF9pZCI6MTc4NjMxMTU5NDM1OTk3MTQsImFzc2V0X2FnZV9kYXlzIjoxNCwidmlfdXNlY2FzZV9pZCI6MTAwOTksImR1cmF0aW9uX3MiOjksInVybGdlbl9zb3VyY2UiOiJ3d3cifQ%3D%3D&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=96ee04eb4ea194d4&_nc_vs=HBksFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HRUpOY1NZMVgzQ0k4NWNEQUEyV3JzWEw5Ym9kYnNwVEFRQUYVAALIARIAFQIYUWlnX3hwdl9wbGFjZW1lbnRfcGVybWFuZW50X3YyLzc4NDM4RTk1MjZDREE2NUFERkI4MURCRTFCMTlEQUFDX2F1ZGlvX2Rhc2hpbml0Lm1wNBUCAsgBEgAoABgAGwKIB3VzZV9vaWwBMRJwcm9ncmVzc2l2ZV9yZWNpcGUBMRUAACbE-bKGmZq7PxUCKAJDMywXQCK7ZFocrAgYEmRhc2hfYmFzZWxpbmVfMV92MREAdf4HZeadAQA&oh=00_Afzx65j7yI2sz7Ho40aTbr64F3tlp6_AZnjxu8YZntv2YA&oe=69C06D7C",
            "alt": null,
            "likesCount": 254,
            "videoViewCount": 6043,
            "timestamp": "2026-03-04T03:06:46.000Z",
            "childPosts": [],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "productType": "clips",
            "taggedUsers": [
              {
                "full_name": "R I D D H I   M Y A T R A (म्यात्रा)",
                "id": "44242663472",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.82787-19/590543153_18087099476479473_7650689792935331439_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=109&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=C8MO2y4_1YkQ7kNvwFCo-vj&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afwqf70Al47-y_SyTt7bUBCmtyTx65tf_NNC9h_LGu7XXQ&oe=69C077A3&_nc_sid=8b3546",
                "username": "pxintales"
              },
              {
                "full_name": "Archi Jain",
                "id": "5789571140",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-1.cdninstagram.com/v/t51.82787-19/610189149_18389838961195141_1688913760359741680_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-1.cdninstagram.com&_nc_cat=101&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=qUPw_aebsi8Q7kNvwEVuHYS&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzFBuXoL1UU8-lsdcQC7wbp-S9ZweAyqF7lKNZZ4YhEYg&oe=69C04FD2&_nc_sid=8b3546",
                "username": "_archiii.__"
              },
              {
                "full_name": "Shaleen Poddar",
                "id": "9130508435",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.2885-19/500442939_18195571684308436_2489791778997007579_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=104&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=eWOW5v42ISkQ7kNvwHCp68b&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyXh7fC7IAJr57jTFT6sJcWW7JHVYG1MJ7JlN1RH8D37Q&oe=69C083F2&_nc_sid=8b3546",
                "username": "shaleen_poddar"
              },
              {
                "full_name": "Shubham Kothari'",
                "id": "7154642290",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.82787-19/555547303_18326218453234291_5905247825401919863_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=107&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=0oINYhehz0UQ7kNvwFJXDX7&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzFZDILf69_4dNZT0AZgpTUyGdEY87YjIFxDYcDBgxNQw&oe=69C07E55&_nc_sid=8b3546",
                "username": "kothari_shub"
              },
              {
                "full_name": "Puru Agarwal",
                "id": "2201900786",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.2885-19/300242437_174699415052802_3501114932006103194_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4zOTcuYzIifQ&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=106&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=cJ2_LIjG-VYQ7kNvwEQvJOe&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afwb_M19BRs6zGJUVh3RmtTR9AClRCh82r4M_9lAWXdD6Q&oe=69C07DB5&_nc_sid=8b3546",
                "username": "agarwal_puru"
              },
              {
                "full_name": "Rishil..",
                "id": "1593155024",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.2885-19/456210529_1160936371683821_7090149528622724016_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=105&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=B7wS2cC6HIkQ7kNvwHDkkse&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afwl8u1B6J2EPLK6QsnWl24Yra4jfrcP3JfBqrS1_g908g&oe=69C051D7&_nc_sid=8b3546",
                "username": "rishilshah_"
              },
              {
                "full_name": "ₘᵤₖᵤₗ",
                "id": "5448092197",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-19/587594216_18386651884180198_363645490824546726_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=110&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=XeZP6PE4MXQQ7kNvwGiPvpC&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afwb2hS2hsB7OI_uYY3PyBocfy7jbucIfL1xRePchyiLKA&oe=69C05568&_nc_sid=8b3546",
                "username": "mukul.devjani"
              },
              {
                "full_name": "Paritosh Malwiya",
                "id": "788765103",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.2885-19/54247630_2956130077734043_4431014126017314816_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=107&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=TOhpl1nfcAkQ7kNvwFY6jZG&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfwVkKDwavnsvzLR_eilLunw8uI-uaSfAHw7E8BfP1Nw5Q&oe=69C0662A&_nc_sid=8b3546",
                "username": "paritosh_57"
              }
            ],
            "musicInfo": {
              "artist_name": "Bally Jagpal",
              "song_name": "Aaja Sohneya (feat. Shazia Manzoor)",
              "uses_original_audio": false,
              "should_mute_audio": false,
              "should_mute_audio_reason": "",
              "audio_id": "1599685697052135"
            },
            "isCommentsDisabled": false
          },
          {
            "id": "3844731116343267080",
            "type": "Video",
            "shortCode": "DVbOpR-iA8I",
            "caption": "Where ambition meets discipline, founders, fresh miles, meaningful conversations, and specialty coffee at GAG Coffee Co. fueling what’s next.\n\nIn collaboration with @frcsurat\n\n📍 GAG Coffee Co., Groundfloor, SNS Arista, Vesu.\n\n[ gag, gagcoffeeco, coffee, coffeshop, cafe in gift city, gift city, gandhinagar, best coffee, New Outlet, Surat, VR Mall, IIM Road , HL college , VR surat, Vesu, running with coffee, cafe run]\n\n#gag #gagcoffeeco #coffeeshop #specialitycoffee #running",
            "hashtags": [
              "gag",
              "gagcoffeeco",
              "coffeeshop",
              "specialitycoffee",
              "running"
            ],
            "mentions": [
              "frcsurat"
            ],
            "url": "https://www.instagram.com/p/DVbOpR-iA8I/",
            "commentsCount": 3,
            "dimensionsHeight": 1137,
            "dimensionsWidth": 640,
            "displayUrl": "https://scontent-ord5-3.cdninstagram.com/v/t51.82787-15/648207348_17886487224452035_8652113179568421494_n.jpg?stp=dst-jpg_e15_tt6&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=107&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=fQfBdhiNpMIQ7kNvwHPnKFx&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzU15lsb-E-WktfCeHiMnm1bEgMpJyiueLUklpjfsm2Ug&oe=69C05F0A&_nc_sid=8b3546",
            "images": [],
            "videoUrl": "https://scontent-ord5-1.cdninstagram.com/o1/v/t16/f2/m69/AQPctCAp36FxLI0DFh-EQj7KoDqcCmP0lB2MHukmC66I3eidpdHHQ_f_SnSfEfvO3idc_7W2_QCBzblz25sdEhwi.mp4?strext=1&_nc_cat=111&_nc_sid=5e9851&_nc_ht=scontent-ord5-1.cdninstagram.com&_nc_ohc=TPB5X-veMYoQ7kNvwG1iArm&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0xJUFMuQzMuNzIwLmRhc2hfYmFzZWxpbmVfMV92MSIsInhwdl9hc3NldF9pZCI6MTc4ODYxOTQ5OTc0NTIwMzUsImFzc2V0X2FnZV9kYXlzIjoxNCwidmlfdXNlY2FzZV9pZCI6MTAwOTksImR1cmF0aW9uX3MiOjI2LCJ1cmxnZW5fc291cmNlIjoid3d3In0%3D&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=ff7c79d07d72c40b&_nc_vs=HBksFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HR0VuaHlZV0psUWFPZFFKQUVJWnRTQUVZZkZEYnNwVEFRQUYVAALIARIAFQIYUWlnX3hwdl9wbGFjZW1lbnRfcGVybWFuZW50X3YyLzlBNEZBRkMyRDg5NUI5QzE2OTU1MTQxMzMwQTc3MEExX2F1ZGlvX2Rhc2hpbml0Lm1wNBUCAsgBEgAoABgAGwKIB3VzZV9vaWwBMRJwcm9ncmVzc2l2ZV9yZWNpcGUBMRUAACaG9KazydnFPxUCKAJDMywXQDpMzMzMzM0YEmRhc2hfYmFzZWxpbmVfMV92MREAdf4HZeadAQA&oh=00_AfyE2UKbHro_levvgdp7jXN-0xzo0Cmnlhe3x_wS5FQL5Q&oe=69C05071",
            "alt": null,
            "likesCount": 106,
            "videoViewCount": 17953,
            "timestamp": "2026-03-03T14:22:23.000Z",
            "childPosts": [],
            "ownerUsername": "gagcoffeeco_india",
            "ownerId": "73447844034",
            "productType": "clips",
            "taggedUsers": [
              {
                "full_name": "FRC Surat",
                "id": "77819791713",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-19/571265983_17843245041599714_7567859433107320002_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby41NTUuYzIifQ&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=2SdPT5Ay0VEQ7kNvwFb6Y9U&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyzKSn4DjqmL64qiZEW9SsGU2U-7Mv1mQHtk9dc3KDnVA&oe=69C0726C&_nc_sid=8b3546",
                "username": "frcsurat"
              }
            ],
            "musicInfo": {
              "artist_name": "Bruno Mars",
              "song_name": "I Just Might",
              "uses_original_audio": false,
              "should_mute_audio": false,
              "should_mute_audio_reason": "",
              "audio_id": "1403587441240226"
            },
            "isCommentsDisabled": false
          },
          {
            "id": "3843005661483420253",
            "type": "Video",
            "shortCode": "DVVGUm6iMpd",
            "caption": "Sub 30 morning with the crew💪🏽🔥\n\n#reelitfeelit \n#fitness \n#running \n#runclub \n#morningmotivation",
            "hashtags": [
              "reelitfeelit",
              "fitness",
              "running",
              "runclub",
              "morningmotivation"
            ],
            "mentions": [],
            "url": "https://www.instagram.com/p/DVVGUm6iMpd/",
            "commentsCount": 21,
            "dimensionsHeight": 1920,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/640384795_18399468076180198_1444496327224731797_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=110&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=Dp6y2N-HZbkQ7kNvwEtQrQZ&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfwDCQtpBGgvl20RTSKGnV9avCUn3P2opjJHrR-vPi8hmA&oe=69C06E3A&_nc_sid=8b3546",
            "images": [],
            "videoUrl": "https://scontent-ord5-2.cdninstagram.com/o1/v/t2/f2/m86/AQOqibyojn5mOSqjJ_qWJlOfZ0ax6XQ8Lw4qo4jSOyXvVpmk6SAt9wFbykO2lkUlRqnzGR_RkqCCaS32dSHnMIRo5444bSgmF6aAiew.mp4?_nc_cat=105&_nc_sid=5e9851&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_ohc=5k_I3G4fSLQQ7kNvwHfy9FF&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0xJUFMuQzMuNzIwLmRhc2hfYmFzZWxpbmVfMV92MSIsInhwdl9hc3NldF9pZCI6MTc5NDY1NTY5MjkxMTUyNDIsImFzc2V0X2FnZV9kYXlzIjoxNywidmlfdXNlY2FzZV9pZCI6MTA4MjcsImR1cmF0aW9uX3MiOjgsInVybGdlbl9zb3VyY2UiOiJ3d3cifQ%3D%3D&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=9095666e05021ed1&_nc_vs=HBksFQIYUmlnX3hwdl9yZWVsc19wZXJtYW5lbnRfc3JfcHJvZC9ERTQ3ODhDNUFGOTcwRjY4NTUxRjU5NDAwRUMwQjM5Rl92aWRlb19kYXNoaW5pdC5tcDQVAALIARIAFQIYUWlnX3hwdl9wbGFjZW1lbnRfcGVybWFuZW50X3YyL0IxNEYxRUMwMDFCMUUxQzAxOTY1NTUxMURDRUU2QTk1X2F1ZGlvX2Rhc2hpbml0Lm1wNBUCAsgBEgAoABgAGwKIB3VzZV9vaWwBMRJwcm9ncmVzc2l2ZV9yZWNpcGUBMRUAACbUkZzrjJPhPxUCKAJDMywXQCB3S8an754YEmRhc2hfYmFzZWxpbmVfMV92MREAdf4HZZapAQA&oh=00_AfwP-JYh1IpnsQ2f7LQLKbxiVe7RGl4qaHjAZ3p8znP6Ag&oe=69BC853A",
            "alt": null,
            "likesCount": 149,
            "videoViewCount": 3104,
            "timestamp": "2026-03-01T05:19:56.000Z",
            "childPosts": [],
            "locationName": "Surat, Gujarat",
            "locationId": "213155017",
            "ownerUsername": "mukul.devjani",
            "ownerId": "5448092197",
            "productType": "clips",
            "taggedUsers": [
              {
                "full_name": "FRC Surat",
                "id": "77819791713",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-19/571265983_17843245041599714_7567859433107320002_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby41NTUuYzIifQ&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=2SdPT5Ay0VEQ7kNvwFb6Y9U&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyzKSn4DjqmL64qiZEW9SsGU2U-7Mv1mQHtk9dc3KDnVA&oe=69C0726C&_nc_sid=8b3546",
                "username": "frcsurat"
              },
              {
                "full_name": "Rishil..",
                "id": "1593155024",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.2885-19/456210529_1160936371683821_7090149528622724016_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=105&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=B7wS2cC6HIkQ7kNvwHDkkse&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afwl8u1B6J2EPLK6QsnWl24Yra4jfrcP3JfBqrS1_g908g&oe=69C051D7&_nc_sid=8b3546",
                "username": "rishilshah_"
              },
              {
                "full_name": "Pooshan Vyas",
                "id": "542690576",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.2885-19/465522264_1450314472331638_8214743279384431677_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=110&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=ilQh5fS8tLIQ7kNvwEW6K4x&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzgDNehVe1Qdkwd4Rd1vhz8RyRErn3eBsVFzuQRF7p3Cw&oe=69C07C69&_nc_sid=8b3546",
                "username": "pooshanv"
              }
            ],
            "isCommentsDisabled": false
          },
          {
            "id": "3841504140640184749",
            "type": "Image",
            "shortCode": "DVPw6mqDOmt",
            "caption": "SAME CREW. NEW COORDINATES.📍⚡️\n\nWe’re ending February by shifting the map. We’ve got a brand new start line at GAG Coffee Co. and we’re moving the clock up to beat the heat.\n\n• NEW SPOT: @gagcoffeeco_india \n• THE HUDDLE: 6:15 AM\n• THE START: 6:30 AM\n\nFirst time? There’s no better Saturday to debut. The route is new for everyone, the vibe is high-voltage, and we’ve got a spot for every pace.\n\nNew ground to cover. New PRs to chase. Set your alarms, we’re not waiting for the sun. 🏃‍♂️💨",
            "hashtags": [],
            "mentions": [
              "gagcoffeeco_india"
            ],
            "url": "https://www.instagram.com/p/DVPw6mqDOmt/",
            "commentsCount": 8,
            "dimensionsHeight": 1349,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/641758472_17862178476599714_5729290745156745315_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=LjRcdv5mRXoQ7kNvwGy1I6C&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&ig_cache_key=Mzg0MTUwNDE0MDY0MDE4NDc0OQ%3D%3D.3-ccb7-5&oh=00_AfxHw78XSkrIwAsDyLNYiRHVWKjbSvvKCopuWSVWEvKAzw&oe=69C05A5E&_nc_sid=8b3546",
            "images": [],
            "alt": "Photo by FRC Surat on February 26, 2026. May be an image of ‎poster and ‎text that says '‎CSTV 0 STV 28-02-2026 MG Any pace. مسل be there 6:30M @GAG Coffee CO.‎'‎‎.",
            "likesCount": 43,
            "timestamp": "2026-02-27T03:30:00.000Z",
            "childPosts": [],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "isCommentsDisabled": false
          },
          {
            "id": "3838907322320475035",
            "type": "Video",
            "shortCode": "DVGid7IjtOb",
            "caption": "This is your sign to join us⚡️\n\n#foundersrunningclub #frcsurat #runnerscommunity #surat #runningcommunity",
            "hashtags": [
              "foundersrunningclub",
              "frcsurat",
              "runnerscommunity",
              "surat",
              "runningcommunity"
            ],
            "mentions": [],
            "url": "https://www.instagram.com/p/DVGid7IjtOb/",
            "commentsCount": 23,
            "dimensionsHeight": 1136,
            "dimensionsWidth": 640,
            "displayUrl": "https://scontent-ord5-3.cdninstagram.com/v/t51.71878-15/640297483_1877369886240976_7328779009685136019_n.jpg?stp=dst-jpg_e15_tt6&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=104&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=b3iB66TSdc0Q7kNvwEnJDwI&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfwPkjk81Fvfv-kXQA2eMoa7BrvvS0xHDeTnwhtYxAmObA&oe=69C070CA&_nc_sid=8b3546",
            "images": [],
            "videoUrl": "https://scontent-ord5-3.cdninstagram.com/o1/v/t16/f2/m69/AQN7HTwq0NvuPPvBhOustVGmty6os-yt5M5C_lZFNwmEd129qpGdK_iIBdWu8LCY-xz4hQba1fwtrsOnIEqCtaAy.mp4?strext=1&_nc_cat=104&_nc_sid=5e9851&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_ohc=MAFiSJJegBYQ7kNvwFds7r3&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0xJUFMuQzMuNzIwLmRhc2hfYmFzZWxpbmVfMV92MSIsInhwdl9hc3NldF9pZCI6MTc4NjE2NzY5MDM1OTk3MTQsImFzc2V0X2FnZV9kYXlzIjoyMiwidmlfdXNlY2FzZV9pZCI6MTAwOTksImR1cmF0aW9uX3MiOjExLCJ1cmxnZW5fc291cmNlIjoid3d3In0%3D&ccb=17-1&vs=fbb7a2b2a7ecc34&_nc_vs=HBksFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HTE1KLXlYVXRWQkc0TjRHQUM4QjFqVGtjODRhYnNwVEFRQUYVAALIARIAFQIYUWlnX3hwdl9wbGFjZW1lbnRfcGVybWFuZW50X3YyL0Y3NERGMzhFODA2NEYyQzQ1M0UzNjlEQjRDMzFBRkIwX2F1ZGlvX2Rhc2hpbml0Lm1wNBUCAsgBEgAoABgAGwKIB3VzZV9vaWwBMRJwcm9ncmVzc2l2ZV9yZWNpcGUBMRUAACbEufSut8a6PxUCKAJDMywXQCfMzMzMzM0YEmRhc2hfYmFzZWxpbmVfMV92MREAdf4HZeadAQA&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&oh=00_Afxjn8GbThe0ykAsu9c05wIbRJj1VmpGbVEt38zGs8rzFA&oe=69C06C5C",
            "alt": null,
            "likesCount": 198,
            "videoViewCount": 7572,
            "timestamp": "2026-02-23T13:30:00.000Z",
            "childPosts": [],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "productType": "clips",
            "taggedUsers": [
              {
                "full_name": "Founders Running Club",
                "id": "54103679926",
                "is_verified": true,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.2885-19/484075393_971581061779406_1356255192789126684_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby41NTUuYzIifQ&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=RgvAJvueBroQ7kNvwGtohBy&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyWdaaLH1Lb0Jk5oYpSKj7lL5vHbzkV4Z4CVWopEceEtg&oe=69C0512B&_nc_sid=8b3546",
                "username": "foundersrc"
              },
              {
                "full_name": "Archi Jain",
                "id": "5789571140",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-1.cdninstagram.com/v/t51.82787-19/610189149_18389838961195141_1688913760359741680_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-1.cdninstagram.com&_nc_cat=101&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=qUPw_aebsi8Q7kNvwEVuHYS&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzFBuXoL1UU8-lsdcQC7wbp-S9ZweAyqF7lKNZZ4YhEYg&oe=69C04FD2&_nc_sid=8b3546",
                "username": "_archiii.__"
              },
              {
                "full_name": "Shaleen Poddar",
                "id": "9130508435",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.2885-19/500442939_18195571684308436_2489791778997007579_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=104&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=eWOW5v42ISkQ7kNvwHCp68b&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyXh7fC7IAJr57jTFT6sJcWW7JHVYG1MJ7JlN1RH8D37Q&oe=69C083F2&_nc_sid=8b3546",
                "username": "shaleen_poddar"
              },
              {
                "full_name": "Puru Agarwal",
                "id": "2201900786",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.2885-19/300242437_174699415052802_3501114932006103194_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4zOTcuYzIifQ&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=106&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=cJ2_LIjG-VYQ7kNvwEQvJOe&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afwb_M19BRs6zGJUVh3RmtTR9AClRCh82r4M_9lAWXdD6Q&oe=69C07DB5&_nc_sid=8b3546",
                "username": "agarwal_puru"
              }
            ],
            "musicInfo": {
              "artist_name": "Eminem",
              "song_name": "Rap God",
              "uses_original_audio": false,
              "should_mute_audio": false,
              "should_mute_audio_reason": "",
              "audio_id": "343761702945851"
            },
            "isCommentsDisabled": false
          },
          {
            "id": "3836040687234655458",
            "type": "Image",
            "shortCode": "DU8Wq5KkSTi",
            "caption": "દોડો • दौड़ो • DHAVA • RUN\n\nDiversity is our strength; the run is our connection. 🤝🏃‍♀️\nIn honor of International Mother Language Day, this Saturday’s FRC run is a tribute to our heritage and our hustle. Let’s hit the road and celebrate the many voices that drive our community forward.\n\nLet’s make every kilometer count.\n\n🗓️ Date: 21.02.2026\n📍 Starting Point: @pakikisurat (Pakiki, Piplod)\n⏰ Start Time: 7:00 AM",
            "hashtags": [],
            "mentions": [
              "pakikisurat"
            ],
            "url": "https://www.instagram.com/p/DU8Wq5KkSTi/",
            "commentsCount": 1,
            "dimensionsHeight": 1349,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/640039511_17861097600599714_7932066468205862619_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=rro4cpx2onIQ7kNvwHQCoSX&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&ig_cache_key=MzgzNjA0MDY4NzIzNDY1NTQ1OA%3D%3D.3-ccb7-5&oh=00_AfySUZlnGZHXU-gnLYj4hvzzEDzHNOx1IowLDVHVLANc9g&oe=69C05912&_nc_sid=8b3546",
            "images": [],
            "alt": "Photo by FRC Surat on February 19, 2026. May be an image of heart, poster and text that says 'C CSTV STV 21-02-2026 Celebrate the language of your ofyourheart heart with the strength of your legs. 6:50AM @PAKKI,Piplo @PAKKI, Piplod'.",
            "likesCount": 36,
            "timestamp": "2026-02-19T14:36:28.000Z",
            "childPosts": [],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "isCommentsDisabled": false
          },
          {
            "id": "3831370192861816491",
            "type": "Image",
            "shortCode": "DUrwuQUjGar",
            "caption": "Who’s your pace partner? 👟❤️\n\nThis Saturday, we’re switching things up for Valentine’s Day. No solo miles,we’re running in pairs!\n\nWhether you’re bringing your favorite running buddy, a partner, or showing up solo to be paired with someone new on the spot, this run is all about community and connection.\n\n• ⏰ 6:50 AM: Pair up & Warm up\n• 🏁 7:00 AM: We hit the pavement\n• 📍 Location: @pakikisurat , Piplod \n\n#valentinespecial❤️ #frcsurat #foundersrunningclub #pacepartner #runningcommunity",
            "hashtags": [
              "valentinespecial❤️",
              "frcsurat",
              "foundersrunningclub",
              "pacepartner",
              "runningcommunity"
            ],
            "mentions": [
              "pakikisurat"
            ],
            "url": "https://www.instagram.com/p/DUrwuQUjGar/",
            "commentsCount": 5,
            "dimensionsHeight": 1349,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/633543235_17860109421599714_5832564797333143839_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=l6tUWu0JgPQQ7kNvwFYNygN&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&ig_cache_key=MzgzMTM3MDE5Mjg2MTgxNjQ5MQ%3D%3D.3-ccb7-5&oh=00_AfxSijquhIAlR4B_NNeKFhMq2vyEUE6z0sZp9wH3t-31cQ&oe=69C05E47&_nc_sid=8b3546",
            "images": [],
            "alt": "Photo by FRC Surat on February 12, 2026. May be a Twitter screenshot of one or more people, television, card, magazine, screen, poster and text that says 'SATURDAY 14-02-2026 02- 07:00 07 AM No Solo Miles Bring a +1 or find your pair the start line. Who's Your pace partner? at BOUEE A STV STV See you all at Pakiki, Piplod'.",
            "likesCount": 53,
            "timestamp": "2026-02-13T03:59:06.000Z",
            "childPosts": [],
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "isCommentsDisabled": false
          },
          {
            "id": "3828488744978886544",
            "type": "Video",
            "shortCode": "DUhhjq3DC-Q",
            "caption": "🤷🏻‍♀️🤷🏻‍♀️\n\n#frcsurat #foundersrunningclub #runnerscommunity #ekalrun #marathon",
            "hashtags": [
              "frcsurat",
              "foundersrunningclub",
              "runnerscommunity",
              "ekalrun",
              "marathon"
            ],
            "mentions": [],
            "url": "https://www.instagram.com/p/DUhhjq3DC-Q/",
            "commentsCount": 10,
            "dimensionsHeight": 1921,
            "dimensionsWidth": 1080,
            "displayUrl": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-15/631584678_17859561075599714_937876120832850024_n.jpg?stp=dst-jpg_e35_p1080x1080_sh0.08_tt6&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=102&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=dmH5dBRhT0sQ7kNvwGAh1iS&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afyr5lW5OUCtZKXbjoNdZkFgTLVXdE1graoxWRTIQK9C9g&oe=69C05683&_nc_sid=8b3546",
            "images": [],
            "videoUrl": "https://scontent-ord5-2.cdninstagram.com/o1/v/t16/f2/m69/AQP1aCyuieiqJn9KOvCzO0eNKrcjSKy9m_Sw5iLmD-k7DUunMH86UtNuoEiNEBddX2QKmDIybs_zq-pwC5NChces.mp4?strext=1&_nc_cat=110&_nc_sid=5e9851&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_ohc=DWsRaSaAnTIQ7kNvwEdn7LX&efg=eyJ2ZW5jb2RlX3RhZyI6Inhwdl9wcm9ncmVzc2l2ZS5JTlNUQUdSQU0uQ0xJUFMuQzMuNzIwLmRhc2hfYmFzZWxpbmVfMV92MSIsInhwdl9hc3NldF9pZCI6MTc4NTk1NjA4ODY1OTk3MTQsImFzc2V0X2FnZV9kYXlzIjozNywidmlfdXNlY2FzZV9pZCI6MTAwOTksImR1cmF0aW9uX3MiOjgsInVybGdlbl9zb3VyY2UiOiJ3d3cifQ%3D%3D&ccb=17-1&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&_nc_ss=1a&_nc_zt=28&vs=7aaf145e63ec9148&_nc_vs=HBksFQIYOnBhc3N0aHJvdWdoX2V2ZXJzdG9yZS9HTXF0aFNVNzZPVktMX3NHQUJ0b0tJdi1hYkVJYnNwVEFRQUYVAALIARIAFQIYUWlnX3hwdl9wbGFjZW1lbnRfcGVybWFuZW50X3YyLzg1NDBFNTNFN0NGNUUxQzdBNTRDMzIwOEU5NjJGN0IxX2F1ZGlvX2Rhc2hpbml0Lm1wNBUCAsgBEgAoABgAGwKIB3VzZV9vaWwBMRJwcm9ncmVzc2l2ZV9yZWNpcGUBMRUAACbEgOznocu5PxUCKAJDMywXQCDdsi0OVgQYEmRhc2hfYmFzZWxpbmVfMV92MREAdf4HZeadAQA&oh=00_AfzZkYKMkkp2Ze_QAANDY-BPCjgAsL6beclitFES9KtFLg&oe=69C07672",
            "alt": null,
            "likesCount": 196,
            "videoViewCount": 11420,
            "timestamp": "2026-02-09T04:31:56.000Z",
            "childPosts": [],
            "locationName": "Surat, Gujarat",
            "locationId": "213155017",
            "ownerUsername": "frcsurat",
            "ownerId": "77819791713",
            "productType": "clips",
            "taggedUsers": [
              {
                "full_name": "Ekal Run",
                "id": "64159688396",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.2885-19/500305874_17909053848136397_3606099916265088027_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=105&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=-O1wuSkiDPEQ7kNvwHUZwtq&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfzT0r1BmWpmkOo1k9bRw1te23FeTisY09Ne_Mv_Pa5y8w&oe=69C06A0A&_nc_sid=8b3546",
                "username": "ekalrunsurat"
              },
              {
                "full_name": "Shaleen Poddar",
                "id": "9130508435",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-3.cdninstagram.com/v/t51.2885-19/500442939_18195571684308436_2489791778997007579_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-3.cdninstagram.com&_nc_cat=104&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=eWOW5v42ISkQ7kNvwHCp68b&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_AfyXh7fC7IAJr57jTFT6sJcWW7JHVYG1MJ7JlN1RH8D37Q&oe=69C083F2&_nc_sid=8b3546",
                "username": "shaleen_poddar"
              },
              {
                "full_name": "ₘᵤₖᵤₗ",
                "id": "5448092197",
                "is_verified": false,
                "profile_pic_url": "https://scontent-ord5-2.cdninstagram.com/v/t51.82787-19/587594216_18386651884180198_363645490824546726_n.jpg?stp=dst-jpg_e0_s150x150_tt6&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLmRqYW5nby4xMDgwLmMyIn0&_nc_ht=scontent-ord5-2.cdninstagram.com&_nc_cat=110&_nc_oc=Q6cZ2QGNxPvBlQf7ukzctlaoRA8F5WimbTxv70B2XlaPh3wAu3X62zShlx8hH6ZTSxAekrQ&_nc_ohc=XeZP6PE4MXQQ7kNvwGiPvpC&_nc_gid=fHU4ah1rM8tL65c93J-PnQ&edm=AOQ1c0wBAAAA&ccb=7-5&oh=00_Afwb2hS2hsB7OI_uYY3PyBocfy7jbucIfL1xRePchyiLKA&oe=69C05568&_nc_sid=8b3546",
                "username": "mukul.devjani"
              }
            ],
            "musicInfo": {
              "artist_name": "Tate McRae",
              "song_name": "Just Keep Watching",
              "uses_original_audio": false,
              "should_mute_audio": false,
              "should_mute_audio_reason": "",
              "audio_id": "530635093317808"
            },
            "isCommentsDisabled": false
          }
        ],
        "fbid": "17841477991397473"
      }
    ],
    "Edit Fields": [
      {
        "Hairstage": "Founders in Surat"
      }
    ],
    "Normalize Data": [
      {
        "id": "77819791713",
        "username": "frcsurat",
        "fullName": "FRC Surat",
        "bio": "Surat 🇮🇳 chapter of @foundersrc\nSocial miles for builders, operators and investors\nBy @shaleen_poddar @_archiii.__ and @agarwal_puru",
        "followers": 806,
        "following": 4,
        "postsCount": 33,
        "profileUrl": "https://www.instagram.com/frcsurat",
        "website": "http://foundersrc.com/",
        "category": "Community",
        "locationName": "Surat, Gujarat",
        "isHighQuality": "Yes",
        "lastActive": "2026-03-14T13:34:16.000Z"
      }
    ],
    "Edit Fields1": [
      {
        "User Name": "frcsurat",
        "Full Name": "FRC Surat",
        "Bio": "Surat 🇮🇳 chapter of @foundersrc\nSocial miles for builders, operators and investors\nBy @shaleen_poddar @_archiii.__ and @agarwal_puru",
        "Followers": 806,
        "Following": 4,
        "Post Count": 33,
        "URL": "https://www.instagram.com/frcsurat",
        "Website": "http://foundersrc.com/",
        "Location": "Surat, Gujarat",
        "High Quality": "Yes",
        "Active Status": "2026-03-14T13:34:16.000Z"
      }
    ],
    "Save Leads in Instragram Leads": [
      {
        "User Name": "frcsurat",
        "Full Name": "FRC Surat",
        "Bio": "Surat 🇮🇳 chapter of @foundersrc\nSocial miles for builders, operators and investors\nBy @shaleen_poddar @_archiii.__ and @agarwal_puru",
        "Followers": 806,
        "Post Count": 33,
        "Following": 4,
        "URL": "https://www.instagram.com/frcsurat",
        "Website": "http://foundersrc.com/",
        "Location": "Surat, Gujarat",
        "High Quality": "Yes",
        "Active Status": "2026-03-14T13:34:16.000Z"
      }
    ]
  },
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "86d81f670c962873ff00838d1f71a4d53552e327fd745f40e6a14070c5e01c4c"
  }
}
