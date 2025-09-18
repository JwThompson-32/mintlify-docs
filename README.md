# Mintlify Starter Kit

Use the starter kit to get your docs deployed and ready to customize.

Click the green **Use this template** button at the top of this repo to copy the Mintlify starter kit. The starter kit contains examples with

- Hello!
- Navigation
- Customizations
- API reference pages
- Use of popular components

[**Follow the full quickstart guide**](https://starter.mintlify.com/quickstart)

## Development

Install the [Mintlify CLI](https://www.npmjs.com/package/mint) to preview your documentation changes locally. To install, use the following command:

```
npm i -g mint
```

Run the following command at the root of your documentation, where your `docs.json` is located:

```
mint dev
```

View your local preview at `http://localhost:3000`.

## Publishing changes

Install our GitHub app from your [dashboard](https://dashboard.mintlify.com/settings/organization/github-app) to propagate changes from your repo to your deployment. Changes are deployed to production automatically after pushing to the default branch.

## Need help?

### Troubleshooting

- If your dev environment isn't running: Run `mint update` to ensure you have the most recent version of the CLI.
- If a page loads as a 404: Make sure you are running in a folder with a valid `docs.json`.

### Resources

- [Mintlify documentation](https://mintlify.com/docs)

\`\`\`mermaid

sequenceDiagram

    autonumber

    participant App as DivvyWatch App

    participant Bill as [Bill.com](http://Bill.com) API

    participant DB as UFLIP DB

    participant Discord as Discord

    Note over App: Application start

    %% Initialization

    App-\>\>App: Get Public IP

    App-\>\>App: Validate Environment Variables

    App-\>\>App: Initialize [Bill.com](http://Bill.com) API

    App-\>\>App: Initialize UFLIP API

    App-\>\>App: Initialize Discord API

    loop Polling loop (continuous)

        App-\>\>App: Set Transaction Date Range\<br\>(Default: Today)

        App-\>\>Bill: Get new transactions

        Bill--\>\>App: Return transactions

        App-\>\>DB: Open connection

        loop For each transaction page (up to PAGINATION_MAX_PAGES)

            loop For each transaction

                alt Already Processed

                    App-\>\>App: Skip transaction

                else New Transaction

                    App-\>\>Bill: Get card details

                    alt Status: Credit

                        App-\>\>Discord: Send credit alert

                    else Status: Declined

                        App-\>\>Discord: Send declined alert

                    else Status: Authorized or Posted

                        alt Foreign transaction: Yes

                            App-\>\>DB: Search confirmed foreign purchases

                            Note over DB: Match on last4, card type, country, amount and date

                            DB--\>\>App: Return purchases

                        else Foreign transaction: No

                            App-\>\>DB: Search confirmed purchases

                            Note over DB: Match on last4, card type, amount, and date

                            DB--\>\>App: Return purchases

                        end

                        alt No purchases found

                            App-\>\>Bill: Block card

                            Bill--\>\>App: Return result

                            App-\>\>Discord: Send unapproved purchase alert

                        end

                    end

                    App-\>\>App: Update In Memory Cache\<br/\>with Transaction id

                    Note over App: Sleep 100ms before next transaction

                end

            end

            Note over App: Sleep PAGINATION_INTERVAL_MS before next page

        end

        App-\>\>DB: Close connection

        Note over App: Sleep POLL_INTERVAL_MS before next poll

    end

\`\`\`