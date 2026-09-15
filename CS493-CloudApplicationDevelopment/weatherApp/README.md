# Weather App

An Android weather app built with Kotlin that fetches current conditions and a five-day forecast from the OpenWeather API, and remembers previously-viewed cities in a local database so they can be revisited from a navigation drawer.

## Features

- **Current weather screen** — shows temperature, cloud cover, wind speed/direction, and a description/icon for the city currently set in the app's settings.
- **Five-day forecast screen** — shows an extended forecast for the same city.
- **Settings screen** — lets the user change the forecast city and units (standard/metric/imperial).
- **Persistent city history** — every city that's been viewed is saved to a local Room/SQLite database (city name + last-viewed timestamp), so it survives app restarts.
- **Navigation drawer with recent cities** — the drawer lists every saved city, most-recently viewed first, in a `RecyclerView` layered on top of the standard `NavigationView` destinations (Current Weather / Five-Day Forecast / Settings).
- **Tap a city to switch to it** — selecting a city in the drawer updates the "city" preference and reloads the app, so both the current weather and five-day forecast screens reflect the newly selected city.
- **Share** — share the current forecast as text via the Android share sheet.

## Architecture

- **UI**: Jetpack Navigation Component with three fragment destinations (`CurrentWeatherFragment`, `FiveDayForecastFragment`, `SettingsFragment`) hosted in `MainActivity`, plus a custom `DrawerListAdapter` (`RecyclerView`) for the saved-city list in the nav drawer.
- **Networking**: Retrofit + Moshi (`OpenWeatherService`) for calls to the OpenWeather current-weather and five-day-forecast endpoints.
- **Persistence**: Room
  - `CityDatabaseEntry` — entity with `savedCity` (primary key) and `timeStamp`.
  - `CityDatabaseDao` — insert (replace on conflict, so cities are never duplicated), delete, clear-all, and a query for all cities ordered by `timeStamp DESC`.
  - `AppDatabase` — singleton `RoomDatabase`.
  - `BookmarkedCityRepository` — thin wrapper over the DAO.
- **State**: `ViewModel` + `LiveData`/coroutines (`CurrentWeatherViewModel`, `FiveDayForecastViewModel`, `BookmarkedCityViewModel`) to keep network/database calls off the UI thread and survive configuration changes.
- **Images**: Glide, for loading weather condition icons.

## Setup

The app reads the OpenWeather API key from `resValue`, sourced from a Gradle property so it isn't checked into source control:

1. Get an API key from [OpenWeather](https://openweathermap.org/api).
2. In your `~/.gradle/gradle.properties` (create it if it doesn't exist), add:
   ```
   OPENWEATHER_API_KEY="your_api_key_here"
   ```
3. Build and run the app — the key is picked up automatically as `R.string.openweather_api_key`.

## Built with

- Kotlin
- Jetpack Navigation Component, Room, Preference, Lifecycle/ViewModel
- Retrofit + Moshi
- Glide
- Kotlin Coroutines