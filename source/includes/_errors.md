# Errors

| Error Code | Meaning                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 403        | Forbidden: you do not have enough [credits](https://app.platerecognizer.com/products/snapshot-cloud/) to perform this request **or** your API key is wrong.                                                                                                                                                                                                                                                                                                                                                                                                                       |
| 413        | Payload Too Large response status code indicates that the request entity is larger than limits defined by our server. See [upload limits](https://app.platerecognizer.com/upload-limit/).                                                                                                                                                                                                                                                                 |
| 429        | Indicates the user has sent too many requests in a given amount of time. The Free Trial Snapshot API Cloud Plan has a limit of 1 lookup per second.  A Snapshot API Cloud Subscription has a limit of 8 lookups per second.  The Snapshot SDK does not have any lookup limits per second.  [Subscribe](https://app.platerecognizer.com/accounts/plan?utm_source=docs&utm_medium=website) for a higher number of calls per second for your API Cloud plan. The response is `{"detail":"Request was throttled. Expected available in 1 second.","status_code":429}`. |
