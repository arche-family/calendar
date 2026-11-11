# Calendar

The Arche phone's calendar, written in [Arche](https://github.com/arche-family/arche-lang): a WebAssembly component of the Arche phone's world, which the phone has from its first start. That world is Nimadir's mini-app world and what a phone adds to it, here notifications and background wakes; its WIT is in `wit/` (from arche-os's `phone/wit`), and `world` names it. Nimadir's own shell, which has none of them, does not run it.

It works like Android's:

- The month, a grid of its weeks from Monday: today marked, the day picked filled, a day with events shaded. ‹ and › go to the month before and after, and a tap on a day picks it.
- The day picked has its events under the grid, in the order of their times, each with ✕ to delete it.
- A new event is its title, its time set an hour or a quarter of an hour at a time, whether to be reminded of it, and Add.
- An event to be reminded of has the phone wake the app at its time (`background.wake-at`), off the screen or asleep, and the app posts its notification (`notify.post`), its title and time. The phone wakes an app at one time, so the app asks for the soonest reminder, and again once woken; woken, it takes the time it asked for as come, whatever its clock says.

The time is the shell's (`now-millis`), as the user reads it (`utc-offset`). The events, up to when their reminders were posted, and the time the app asked to be woken at are kept in the app's storage. It is an app of the page: its taps come as click events, and its typing as input events.

## Build

With [arche-lang](https://github.com/arche-family/arche-lang) checked out next to this repository and built (`make -C stage0 && make -C compiler && make -C wasm`):

```sh
../arche-lang/wasm/nimadir-app.sh . calendar.component.wasm
```

`manifest` is what the phone's repository packages with the component. It gives the app's name, version and title, and what it may do: keep its data (`storage`), the events.

## Test

`headless` holds the taps and the typing of a headless run, at a time held still (`--now-millis`, 12:00 in Tashkent on 10 November 2026), as the phone's tests have it: an event made for 12:15 with a reminder, the phone asked to wake the app then and, woken (the run is a wake throughout), its notification posted; another without one, its title in Cyrillic; the next month and back; the second deleted. It also holds the functions the phone's world adds, and `expected.txt` what arche-wasm's interpreter said to them, as Nimadir's shell does not run the phone's world. `app-check.sh` runs the app with arche-wasm, by the interpreter and by the JIT, and compares what it says with `expected.txt`. CI runs it on every push.

```sh
../arche-lang/wasm/app-check.sh .
NIMADIR=~/nimadir ../arche-lang/wasm/app-expected.sh .   # expected.txt again, from arche-wasm
```

## License

MIT or Apache-2.0, at your option ([LICENSE](LICENSE)).
