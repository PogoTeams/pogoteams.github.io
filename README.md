# Pogo Teams Update Instructions 
This is the procedure for updating gamemaster and rankings data for Pogo Teams. The developer will have to manually update the gamemaster file, and perform testing which is illustrated below. All data for Pogo Teams is provided via our [bucket repo](https://github.com/PogoTeams/pogoteams.github.io). Pushing data updates to the root of this repo will invoke an update for all instances of the app. Before commiting to an update, we do our testing in the [bucket test subdirectory](https://github.com/PogoTeams/pogoteams.github.io/tree/main/test). Once the testing is approved, we can push the updates to the root.

## Prepare to test new data from PvPoke
1) **Clone or Pull the latest [pogoteams.github.io (our bucket)](https://github.com/PogoTeams/pogoteams.github.io)**

2) **Clone or Pull the latest [PvPoke (our data source)](https://github.com/pvpoke/pvpoke/tree/master/src)**

3) **Copy `gamemaster.json` and the directory `rankings` from `pvpoke/src/data/` into `pogoteams.github.io/test` (our test directory).** *You can overwrite or clear existing data in this test directory, but **leave `timestamp.txt`***.

4) **Specify Open Cups in gamemaster.json** : Update the `openCups` field to specify all cups that should be in this update (json formatting specified below). *Whatever is in the `openCups` field of the `gamemaster.json` will determine which cups are rendered for the app. If no cups are being removed or added, this step can be ignored.*

**"openCups" json value format**
- *The "name" field **MUST** be exactly the name of the directory found in rankings.*

- *The "cp" field is optional and will default to 1500, it's still advised to specify this regardless.*

- *The "include" field is optional, and should always be used when the cup includes a subset of the 18 Pokemon types. The only "filterType" field that is currently used by Pogo Teams is "type", consider this a flag that the specified cup includes a subset of all Pokemon types.*

```json
{
    "name": "name of respective directory in rankings",
    "title": "rendered title for this cup",
    "cp": 1500,
    "include": [{
        "filterType": "type",
        "values": ["rock", "steel", "fighting", "ground"]
    }]
}
```

5) **Remove any unused cups from `test/rankings`** : *Just so that we don't have things that we don't use in there.*

6) Update `test/timestamp.txt` to the current time given this precise format : `yyyy-mm-dd hh:mm:ss` where the time is in military time. *As long as the timestamp is a different time than the last one, updates will be invoked, but it is a good documentation to use relatively accurate times.*

7) **Commit changes to test directory and push to origin** : *Commit summary should be `TEST *` where * is the timestamp currently in `test/timestamp.txt`. It may take up to 10 or so minutes for this update to be reflected on the GitHub Page (our server bucket)*

## Test the new data from PvPoke 
The following testing procedure is illustrated to verify the app is functioning properly before pushing out a new gamemaster and rankings data update.

8) **Clone or Pull the latest [Pogo Teams](https://github.com/PogoTeams/pogo_teams)**

9) **In `lib/pogo_teams_app.dart`, set `testing`  and `forceUpdate` to `true`.** *`forceUpdate` will implicitly invoke an update by resetting the local timestamp, you only need to set this to true if you're going to test the update more than once, which is advised. HOWEVER, it may still be desireable to leave forceUpdate to false the first time, just to make sure the timestamp is actually working.*

10) **Run `flutter run` on a simulator / device of choice** : *Verify that the load screen indicates a `[ TEST ]` update.*

11) **Test the Update**
- *Build a new team for each new cup.**
- *Navigate to rankings, and ensure all new cups are functioning properly.*

12) **In `lib/pogo_teams_app.dart`, set `testing` and `forceUpdate` to `false`.** : *There is no need to commit / push the Pogo Teams app for updates, all model-based updates are pulled from our bucket.*

## Commit and Push to Update App

13) **Copy all contents of `pogoteams.github.io/test` to `pogoteams.github.io`, effectively overwriting the old `timestamp.txt`, `gamemaster.json`, and `rankings` directory.**

14) **Commit and Push `pogoteams.github.io`.** : *Commit summary should be the timestamp currently in `timestamp.txt`.*