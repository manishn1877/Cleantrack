# CleanTrack

Find a public dustbin near you in Bangalore, and add one if it is missing.

CleanTrack began as a first-semester project built around a smart dustbin: a bin with a sensor that measures how full it is. We placed a few of these bins around our neighbourhood and built an app to track them.

Then we asked a simple question. Why use only our own sensor bins? Plenty of shops and offices already have a bin they barely use. If those owners add their bin to the map, people walking nearby can find it and throw their waste there instead of on the road. So we opened the app to the public.

We also tried to use street light poles as bin points. It did not work out because of the compliance rules of BESCOM and the permissions we asked the company for. We kept the bin-owner idea, and this is the result.

## What it does

- **Map and list of bins** near you, with a search for an area or landmark.
- **Wet and dry sides.** Many bins in Bangalore have a wet side and a dry side. Shops and malls often have just one bin, so CleanTrack supports both kinds. A two-sided bin shows each side's fill level. A one-bin spot shows a single level.
- **Add a bin** by taking a photo, scanning a QR sticker on the bin, or placing it by hand on the map.
- **Still there.** Neighbours can confirm that a bin is still in place, once per bin.
- **Report a problem** (overflowing, damaged, not collected, bad smell, missing). Each report is logged on a public record, and the page gives you a ready-to-send message.
- **80% rule.** When a bin, or one side of it, passes 80% full, one ticket is logged. A new one is logged only after it has been emptied.

## Why reports are not sent automatically

The BBMP, which used to take these complaints, has been replaced by the Greater Bengaluru Authority (GBA) and five city corporations. Its single complaints platform, Sahaaya Plus, was still being built in July 2026 reports. We have not found an official email address or form that outside apps can use.

So for now CleanTrack logs the report and gives you the message to send to your ward's city corporation office. Once a corporation gives us an address or form, the backend below can send tickets itself.

## What is real and what is a demo

The website is a front-end page with no server behind it yet. Reports are logged on the page and you send the message yourself. The page's location is real: it uses your device's GPS once you allow it. The bin readings and the ticket history are written into `site/index.html`, so keep them matching your real sensor logs. Photos you take are shown on the page but are not uploaded anywhere.

## Project layout

| Folder | What it is |
| --- | --- |
| `site/index.html` | The whole website as one static file (Map, Add a bin, Report a problem). This is what you put on GitHub Pages |
| `project/Main.dc.html` | The editable design source. `build_site.py` turns it into `site/index.html` |
| `report-service/report_service.py` | Backend that stores bins and fill readings and applies the 80% rule. It dedupes tickets and limits each reporter to 5 per hour. It sends email by SMTP or writes `.eml` files for testing |
| `report-service/test_report_service.py` | 11 tests for the backend |

Run the tests:

```
cd report-service
python3 -m unittest -v
```

Run the service (it needs no packages outside the Python standard library):

```
PORT=8080 API_KEY=change-me WARD_EMAIL=your-real-address@example.org python3 report_service.py
```

Sensors send readings to `POST /api/readings` with the API key. The site sends reports to `POST /api/reports`.

## Putting it on GitHub Pages

1. Create a repository and upload `site/index.html` (you can rename the folder contents to the repo root).
2. In the repository go to Settings, then Pages, choose the `main` branch and the root folder, and save.
3. Open the `https://<your-username>.github.io/<repo>/` link. Pages is served over HTTPS, which browsers require before they will ask for location.

On first visit the browser asks "Allow this site to know your location?". If you allow, bins are sorted by distance and your position shows on the map. If you block it, the site shows the Gandhi Bazaar and National College area and offers a "Use my location" button.

The bin coordinates in `site/index.html` (search for `baseBins`) are approximate points near Gandhi Bazaar and National College. Replace them, and the readings and the ticket list (`history`), with your real data.

## Still to do

- Get a real address or form from a city corporation, and set `WARD_EMAIL`.
- Connect the page to the backend, so new bins, readings and tickets are stored instead of living in the page.
- Add a QR sticker generator for bin owners
