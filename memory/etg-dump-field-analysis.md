# ETG Hotel Dump Field Analysis

**Date:** 2026-02-05
**Source:** `partner_feed_en_v3.jsonl.zst` (last_update: 2026-02-03)
**Purpose:** Complete field inventory before re-indexing — do it once, do it right.

---

## 1. Complete ETG Dump Field Structure

Extracted from 5 sample hotels (smallest + largest). This is every field that exists in the dump:

### Top-Level Fields

| Field | Type | Example | Notes |
|-------|------|---------|-------|
| `id` | string | `"hotel_routeinn_oshu"` | Hotel ID (used as `_id` in OpenSearch) |
| `hid` | int | `6291588` | Numeric hotel ID |
| `name` | string | `"Hotel Route-Inn Oshu"` | Hotel name (language-specific per dump) |
| `address` | string | `"105 Yokocho, Mizusawa-ku, Oshu"` | Full address line |
| `latitude` | float | `39.14122` | |
| `longitude` | float | `141.141` | |
| `star_rating` | int | `3` | 0-5 |
| `postal_code` | string/null | `"023-0801"` | |
| `kind` | string | `"Hotel"` | Hotel type (Hotel, Apartment, Resort, etc.) — **deprecated by RateHawk Jan 2025** but still present |
| `hotel_chain` | string | `"Route Inn"` or `"No chain"` | |
| `email` | string/null | `"oshu@route-inn.co.jp"` | |
| `phone` | string/null | `"+815058477710"` | |
| `is_closed` | bool | `false` | Whether hotel is permanently closed |
| `deleted` | bool | `false` | Whether hotel is deleted from inventory |
| `check_in_time` | string | `"15:00:00"` | HH:MM:SS format |
| `check_out_time` | string | `"10:00:00"` | |
| `front_desk_time_start` | string/null | | Front desk opening |
| `front_desk_time_end` | string/null | | Front desk closing |
| `is_gender_specification_required` | bool | `false` | Regulatory (Japan, Saudi Arabia) |
| `star_certificate` | null/object | | Star rating certification |
| `payment_methods` | string[] | `["visa","mastercard","amex",...]` | |
| `serp_filters` | string[] | `["has_internet","has_parking",...]` | Quick filter tags |
| `images` | string[] | `["https://cdn.worldota.net/t/{size}/content/..."]` | Template URLs with `{size}` |
| `images_ext` | object[] | `[{url, category_slug}]` | Images with category metadata |

### Nested: `region`
| Field | Type | Example |
|-------|------|---------|
| `region.id` | int | `966249413` |
| `region.name` | string | `"Oshu"` |
| `region.country_code` | string | `"JP"` |
| `region.type` | string | `"City"` |
| `region.iata` | string/null | |

### Nested: `amenity_groups[]`
| Field | Type | Example |
|-------|------|---------|
| `amenity_groups[].group_name` | string | `"General"` |
| `amenity_groups[].amenities` | string[] | `["24-hour reception", "Air conditioning"]` |
| `amenity_groups[].non_free_amenities` | string[] | Usually empty `[]` |

### Nested: `room_groups[]`
| Field | Type | Example |
|-------|------|---------|
| `room_groups[].name` | string | `"Bed in Dorm"` |
| `room_groups[].room_group_id` | int | `6` |
| `room_groups[].images` | string[] | Template URLs |
| `room_groups[].room_amenities` | string[] | `["shared-bathroom"]` |
| `room_groups[].rg_ext` | object | `{balcony:0, bathroom:1, bedding:0, bedrooms:0, capacity:0, class:1, club:0, family:0, floor:0, quality:0, sex:0, view:0}` |
| `room_groups[].name_struct.main_name` | string | `"Bed in Dorm"` |
| `room_groups[].name_struct.bathroom` | string/null | `"shared bathroom"` |
| `room_groups[].name_struct.bedding_type` | string/null | |

### Nested: `description_struct[]`
| Field | Type | Example |
|-------|------|---------|
| `description_struct[].title` | string | `"Location"` |
| `description_struct[].paragraphs` | string[] | `["Want to save money while travelling?..."]` |

### Nested: `policy_struct[]`
| Field | Type | Example |
|-------|------|---------|
| `policy_struct[].title` | string | `"Meals"` |
| `policy_struct[].paragraphs` | string[] | Policy text |

### Nested: `metapolicy_struct`
Complex structure with sub-fields:
- `add_fee[]`, `check_in_check_out[]`, `children[]`, `children_meal[]`
- `cot[]`, `deposit[]`, `extra_bed[]`, `internet[]`
- `meal[]`, `parking[]`, `pets[]`, `shuttle[]`
- `no_show {availability, day_period, time}`
- `visa {visa_support}`

### Nested: `metapolicy_extra_info`
| Type | Example |
|------|---------|
| string/null | `"<ul><li>The Japanese Ministry of Health..."` (HTML content) |

### Nested: `facts`
| Field | Type | Example |
|-------|------|---------|
| `facts.rooms_number` | int/null | `149` |
| `facts.floors_number` | int/null | |
| `facts.year_built` | int/null | |
| `facts.year_renovated` | int/null | |
| `facts.register` | object/null | FSA registration |
| `facts.electricity.sockets` | string[] | `["a","b"]` |
| `facts.electricity.voltage` | int[] | `[100]` |
| `facts.electricity.frequency` | int[] | `[50,60]` |

### Nested: `keys_pickup`
| Field | Type | Example |
|-------|------|---------|
| `keys_pickup.type` | string | `"unspecified"` |
| `keys_pickup.is_contactless` | bool | `false` |
| `keys_pickup.apartment_extra_information` | string/null | |
| `keys_pickup.apartment_office_address` | string/null | |
| `keys_pickup.email` | string/null | |
| `keys_pickup.phone` | string/null | |

---

## 2. Comparison: Dump vs. Currently Indexed vs. Needed

### Legend
- ✅ Correctly indexed
- ❌ In dump but NOT indexed
- 🔄 Indexed but needs changes
- 🚫 Not needed

| Dump Field | In Dump? | Currently Indexed? | Frontend Needs? | Verdict | Why |
|---|---|---|---|---|---|
| **id** | ✅ | ✅ `HotelDocumentV2.id` | ✅ | ✅ OK | Primary key |
| **hid** | ✅ | ✅ `HotelDocumentV2.hid` | ✅ | ✅ OK | Numeric ID |
| **name** | ✅ | ✅ | ✅ HotelCard, Header | ✅ OK | |
| **address** | ✅ | 🔄 mapped to `address.street` | ✅ HotelCard shows it | ✅ OK | |
| **latitude/longitude** | ✅ | ✅ as `location.lat/lon` | ✅ Map | ✅ OK | |
| **star_rating** | ✅ | ✅ | ✅ Stars display | ✅ OK | |
| **postal_code** | ✅ | ✅ in `address.postal_code` | Minor | ✅ OK | |
| **region (structured)** | ✅ | 🔄 Only `region_id` extracted | ✅ city from region.name, country_code | **🔄 FIX** | Currently `city` comes from `region.name` and `country_code` from `region.country_code` — works but `region.type` and `region.iata` are lost |
| **hotel_chain** | ✅ | ✅ | Minor (chain filter) | ✅ OK | |
| **check_in_time** | ✅ | ✅ | ✅ HotelDescription | ✅ OK | |
| **check_out_time** | ✅ | ✅ | ✅ HotelDescription | ✅ OK | |
| **is_closed** | ✅ | ✅ as `is_active` | ✅ filtering | ✅ OK | |
| **deleted** | ✅ | ❌ NOT parsed | ✅ Must skip deleted hotels | **❌ ADD** | Hotels with `deleted=true` should be SKIPPED during import. Currently only checking `is_closed`. |
| **kind** | ✅ | ❌ | ⚠️ Deprecated but still useful | **❌ ADD** | Hotel/Apartment/Resort distinction. Frontend has `kind` field. Useful for filtering. Deprecated Jan 2025 but still in dump. |
| **email** | ✅ | ❌ | ✅ HotelContent contact | **❌ ADD** | Currently fetched via ETG API fallback |
| **phone** | ✅ | ❌ | ✅ HotelContent contact | **❌ ADD** | Currently fetched via ETG API fallback |
| **images** | ✅ | ✅ | ✅ Gallery | ✅ OK | |
| **images_ext** | ✅ | ❌ | Nice-to-have (category) | **❌ ADD** | `category_slug` enables better gallery (exterior/room/bathroom/food) |
| **amenity_groups** | ✅ | 🔄 Flattened to `amenities[]` | ✅ Both flat + grouped | **🔄 FIX** | Currently only stores flattened names. Frontend needs both: flat for search filters, grouped for display. Also `non_free_amenities` is lost. |
| **serp_filters** | ✅ | ✅ | ✅ Quick filters | ✅ OK | |
| **payment_methods** | ✅ | ✅ | Minor (display) | ✅ OK | |
| **metapolicy_struct** | ✅ | ✅ as `Value` | ✅ HotelPolicies | ✅ OK | |
| **metapolicy_extra_info** | ✅ | ❌ | ✅ HotelPolicies | **❌ ADD** | HTML content with important notices. Currently null in OS, fetched via ETG API. |
| **description_struct** | ✅ | ❌ | ✅ HotelDescription | **❌ ADD** | **CRITICAL.** Structured description with paragraphs. Currently requires ETG API fallback call. |
| **policy_struct** | ✅ | ❌ | ✅ HotelPolicies | **❌ ADD** | **CRITICAL.** Structured policies. Currently requires ETG API fallback. |
| **room_groups** | ✅ | 🔄 Partial | ✅ RoomCard | **🔄 FIX** | Missing `room_group_id`, `name_struct`, and `size` is not in dump (but see note). Images are there. |
| **room_groups[].room_group_id** | ✅ | ❌ | ✅ Rate matching | **❌ ADD** | Needed for `matchRoomGroupByRgExt()` |
| **room_groups[].name_struct** | ✅ | ❌ | Nice-to-have | 🚫 Skip | Can be derived from name. Low priority. |
| **facts** | ✅ | 🔄 as raw `Value` | ✅ HotelFacts | **🔄 FIX** | Currently stored as raw JSON. Backend expects structured `Facts` with `rating_info`, `poi_distance`, `awards`, `year_opened`, `rooms_quantity`, `floors_quantity`. Dump has **different** structure than what backend expects — see notes. |
| **facts.electricity** | ✅ | ❌ (in `facts` Value) | Minor | 🚫 Skip indexing separately | Rarely displayed. Can stay inside raw facts JSON. |
| **facts.rooms_number** | ✅ | ❌ (in `facts` Value) | ✅ HotelFacts | **🔄 FIX** | Backend expects `rooms_quantity`. Dump has `rooms_number`. Rename needed. |
| **facts.floors_number** | ✅ | In `facts` Value | ✅ HotelFacts | **🔄 FIX** | Backend expects `floors_quantity`. Dump has `floors_number`. |
| **facts.year_built** | ✅ | In `facts` Value | ✅ HotelFacts | **🔄 FIX** | Backend expects `year_opened`. Dump has `year_built`. |
| **facts.year_renovated** | ✅ | In `facts` Value | Minor | 🚫 in raw JSON | Nice to know but not displayed. |
| **region** (structured) | ✅ | ❌ Only `id` used | ✅ | **❌ ADD** | Full region object enables better region display without separate lookup. Store `region.name` in address.city and `region.country_code` in address. |
| **front_desk_time_start** | ✅ | ❌ | Minor | 🚫 Skip | Rarely shown |
| **front_desk_time_end** | ✅ | ❌ | Minor | 🚫 Skip | Rarely shown |
| **keys_pickup** | ✅ | ❌ | Minor | 🚫 Skip | Only relevant for apartments |
| **star_certificate** | ✅ | ❌ | No | 🚫 Skip | Certification metadata, not displayed |
| **is_gender_specification_required** | ✅ | ❌ | Minor | 🚫 Skip | Regulatory, rarely needed |
| **meal_types** | ❌ Not in dump directly | ✅ extracted from metapolicy | Search filters | ✅ OK | Derived field, keep extracting |

---

## 3. What the Frontend/Backend Currently Expects vs. What's Missing

### HotelStaticResponse (GET `/hotels/:id/static`)

| Field | Currently Returned | Source | Gap? |
|-------|-------------------|--------|------|
| id, hid, name | ✅ | OpenSearch | |
| address (structured) | ✅ | OpenSearch | |
| coordinates | ✅ | OpenSearch | |
| star_rating | ✅ | OpenSearch | |
| description | ❌ null | Not in index | **GAP — not in dump either!** Description is only in `description_struct`. Could synthesize from paragraphs. |
| amenities | ✅ | OpenSearch (flat) | |
| images | ✅ | OpenSearch | |
| contacts (phone/email) | ❌ null | Not indexed | **GAP** |
| region_id | ✅ | OpenSearch | |
| country_code | ✅ | OpenSearch | |
| city | ✅ | OpenSearch | |
| **guest_rating** | ❌ NOT in response | Not in HotelStaticResponse struct! | **MAJOR GAP** — Frontend expects it |
| **guest_review_count** | ❌ NOT in response | Not in HotelStaticResponse struct! | **MAJOR GAP** |
| **guest_detailed_ratings** | ❌ NOT in response | Not in HotelStaticResponse struct! | **MAJOR GAP** |

### HotelContentData (GET `/hotels/:id/content`)

| Field | From OpenSearch? | From ETG API Fallback? | Gap? |
|-------|-----------------|----------------------|------|
| room_groups | ✅ Partial | ✅ Full | Missing `room_group_id` from index |
| description_struct | ❌ Not indexed | ✅ via API call | **Adds latency!** Extra API call per hotel view |
| check_in_time | ✅ | ✅ | |
| check_out_time | ✅ | ✅ | |
| facts | ✅ Raw JSON | ✅ | Field name mismatch (dump vs backend model) |
| metapolicy_struct | ✅ | ✅ | |
| metapolicy_extra_info | ❌ | ✅ via API | **Adds latency!** |
| policy_struct | ❌ | ✅ via API | **Adds latency!** |
| amenity_groups | ❌ Flat only | ✅ via API | Loses grouping |
| phone/email | ❌ | ✅ via API | |

### Frontend Components — Field Usage Summary

**HotelCard.tsx** (search results):
- `hotel.name` ✅
- `hotel.address || hotel.city` ✅
- `hotel.stars` ✅
- `hotel.images` ✅
- `hotel.guest_rating` / `hotel.rating` — ⚠️ Comes from reviews, separate flow
- `hotel.guest_review_count` / `hotel.reviewCount` — ⚠️ Same
- `hotel.lowestRate` — From rates API, not from index

**HotelPageClient.tsx** (hotel detail page):
- Everything from HotelCard +
- `hotelContent.description_struct` ❌ Not in index
- `hotelContent.room_groups` ✅ Partial
- `hotelContent.facts` ✅ But field name mismatch
- `hotelContent.metapolicy_struct` ✅
- `hotelContent.metapolicy_extra_info` ❌ Not in index
- `hotelContent.policy_struct` ❌ Not in index
- `hotelContent.check_in_time/check_out_time` ✅
- `hotel.guest_rating`, `guest_detailed_ratings` — From reviews

**RoomCard.tsx** (room cards):
- `rate.roomName`, `rate.amenities`, `rate.roomSize`, `rate.bedType` — All from rates API
- Room images from `room_groups[].images` ✅
- Matched via `rg_ext` ✅

**GuestRatingOverview.tsx**:
- `rating` (0-10 scale) — From reviews index, not hotel dump
- `reviewCount` — From reviews index
- `detailedRatings` (cleanness, location, price, services, room, meal, wifi, hygiene) — From reviews index

---

## 4. Facts Field Name Mapping (Dump → Backend Model)

The dump `facts` structure differs from what the backend `Facts` model expects:

| Dump Field | Backend Model Field | Action |
|-----------|-------------------|--------|
| `facts.rooms_number` | `Facts.rooms_quantity` | **Rename during import** |
| `facts.floors_number` | `Facts.floors_quantity` | **Rename during import** |
| `facts.year_built` | `Facts.year_opened` | **Rename during import** |
| `facts.year_renovated` | *(not in model)* | Store in raw facts or add to model |
| `facts.electricity` | *(not in model)* | Ignore or store in raw facts |
| `facts.register` | *(not in model)* | Ignore |
| *(not in dump)* | `Facts.rating_info` | Populated from **reviews** index |
| *(not in dump)* | `Facts.tripadvisor_id` | External source |
| *(not in dump)* | `Facts.poi_distance` | From **POI** index |
| *(not in dump)* | `Facts.awards` | Not in dump |

---

## 5. Recommendations

### ❌ Fields to ADD to Index (Priority Order)

#### P0 — Critical (eliminate ETG API fallback calls)

1. **`description_struct`** — `Vec<{title, paragraphs}>` — CRITICAL
   - Every hotel detail page currently makes an extra ETG API call just for this
   - It's RIGHT THERE in the dump but we don't index it
   - Saves ~200ms per hotel view + reduces ETG API rate limit usage

2. **`policy_struct`** — `Vec<{title, paragraphs}>` — CRITICAL
   - Same issue as description_struct
   - Displayed in HotelPolicies component

3. **`metapolicy_extra_info`** — `Option<String>` — HIGH
   - HTML content with important guest notices
   - Currently null in OpenSearch, fetched via ETG API

4. **`deleted`** — `bool` — CRITICAL (logic, not field)
   - Hotels with `deleted=true` must be SKIPPED during import
   - Currently we only check `is_closed`

#### P1 — Important (improve data quality)

5. **`amenity_groups`** (structured) — Store BOTH flat `amenities[]` AND structured `amenity_groups[]`
   - Flat list for search filtering (fast)
   - Grouped list for hotel detail display (proper UI grouping)
   - Also preserves `non_free_amenities`

6. **`email`** + **`phone`** → Store in `contact` field
   - Currently null in OpenSearch, every content request fails to show contact
   - Simple strings, minimal index size impact

7. **`room_groups[].room_group_id`** — `i64`
   - Essential for `matchRoomGroupByRgExt()` in frontend
   - Without it, room image matching is unreliable

8. **`images_ext`** — `Vec<{url, category_slug}>`
   - Enables categorized gallery (exterior, room, bathroom, food, etc.)
   - Currently just `images[]` with no category info

9. **`facts` field renaming** — Transform during import:
   - `rooms_number` → `rooms_quantity`
   - `floors_number` → `floors_quantity`
   - `year_built` → `year_opened`
   - Add `year_renovated` to Facts model

#### P2 — Nice to Have

10. **`kind`** — Hotel type classification
    - Deprecated but still in dump and useful for filtering (Hotel vs Apartment vs Hostel)
    - Frontend has `kind` field in Hotel interface
    
11. **`description`** — Synthesize from `description_struct`
    - Concatenate all paragraphs into a single string
    - Used for text search and OpenSearch embedding (neural search)

12. **`region`** (full structured) — Store region metadata
    - Currently only using `region.id` → `region_id`
    - Could store `region.name`, `region.type`, `region.iata` for richer display

### 🚫 Fields to NOT Index

| Field | Why Skip |
|-------|----------|
| `front_desk_time_start/end` | Rarely displayed, marginal value |
| `keys_pickup` | Only relevant for apartments, complex structure |
| `star_certificate` | Internal certification, not displayed |
| `is_gender_specification_required` | Niche regulatory, not displayed |
| `facts.electricity` | Socket info — very niche, keep in raw facts JSON if anything |
| `facts.register` | FSA registration — internal/regulatory |
| `room_groups[].name_struct` | Can derive from `name` — low value, adds complexity |

### ✅ Fields Already Correctly Indexed

| Field | Status |
|-------|--------|
| `id`, `hid`, `name` | ✅ Perfect |
| `address` → `address.street` | ✅ |
| `latitude/longitude` → `location.{lat,lon}` | ✅ |
| `star_rating` | ✅ |
| `region.id` → `region_id` | ✅ |
| `region.country_code` → `address.country_code` | ✅ |
| `region.name` → `address.city` | ✅ |
| `postal_code` → `address.postal_code` | ✅ |
| `check_in_time`, `check_out_time` | ✅ |
| `hotel_chain` | ✅ |
| `is_closed` → `is_active` (inverted) | ✅ |
| `images` | ✅ |
| `serp_filters` | ✅ |
| `payment_methods` | ✅ |
| `metapolicy_struct` (as JSON Value) | ✅ |
| `room_groups` (name, room_amenities, rg_ext, images) | ✅ Partial |
| `amenities` (flattened from amenity_groups) | ✅ For search |
| `meal_types` (derived from metapolicy) | ✅ |

---

## 6. Proposed New `HotelDocumentV2` Structure

```rust
pub struct HotelDocumentV2 {
    // === Existing (keep) ===
    pub id: String,
    pub hid: i64,
    pub name: String,
    pub address: AddressV2,
    pub star_rating: i16,
    pub region_id: i64,
    pub location: GeoPoint,
    pub amenities: Vec<String>,           // Flat list for search
    pub serp_filters: Vec<String>,
    pub room_groups: Vec<RoomGroupV2>,
    pub meal_types: Vec<String>,
    pub check_in_time: Option<String>,
    pub check_out_time: Option<String>,
    pub metapolicy_struct: Option<Value>,
    pub payment_methods: Vec<String>,
    pub hotel_chain: Option<String>,
    pub facts: Option<Value>,              // Keep as Value but transform field names
    pub images: Vec<String>,
    pub is_active: bool,
    pub updated_at: DateTime<Utc>,

    // === NEW FIELDS ===
    pub description_struct: Vec<DescriptionSection>,  // P0
    pub policy_struct: Vec<PolicySection>,             // P0
    pub metapolicy_extra_info: Option<String>,         // P0
    pub amenity_groups: Vec<AmenityGroupV2>,           // P1 (structured)
    pub contact: Option<ContactV2>,                    // P1 (email + phone)
    pub images_ext: Vec<ImageExtV2>,                   // P1 (with category)
    pub kind: Option<String>,                          // P2
    pub description: Option<String>,                   // P2 (synthesized)
}

// Updated RoomGroupV2 with room_group_id
pub struct RoomGroupV2 {
    pub name: Option<String>,
    pub room_group_id: Option<i64>,       // NEW
    pub room_amenities: Vec<String>,
    pub rg_ext: Option<Value>,
    pub images: Vec<String>,
}
```

---

## 7. Import Logic Changes Needed

1. **Skip deleted hotels**: `if hotel.deleted == true { skip }`
2. **Skip is_closed**: `is_active = !is_closed && !deleted`
3. **Transform facts field names**: `rooms_number→rooms_quantity`, `floors_number→floors_quantity`, `year_built→year_opened`
4. **Synthesize description**: Concatenate `description_struct[].paragraphs.join(" ")`
5. **Store amenity_groups AND flatten to amenities**: Both fields in document
6. **Extract contact**: `{phone, email}` from top-level
7. **Store room_group_id**: In each room group
8. **Store images_ext**: Alongside images for category info

---

## 8. Guest Rating / Reviews — Separate Concern

**Important:** `guest_rating`, `guest_review_count`, and `guest_detailed_ratings` come from the **reviews dump**, NOT the hotel dump. They are stored in separate indices (`reviews_en`, `reviews_de`).

The current gap is in the **API layer**: `HotelStaticResponse` doesn't include these fields. The fix is to:
1. Query the reviews index alongside the hotel index
2. Merge `rating_info` into the static response
3. OR: Denormalize — store review summary data IN the hotel document during import

Recommendation: Add `guest_rating`, `guest_review_count` to `HotelDocumentV2` and populate from the reviews dump in a second pass (or merge during import). This eliminates separate review lookups for search results.

---

## 9. Summary of Impact

| Change | Benefit | Effort |
|--------|---------|--------|
| Add `description_struct` | Eliminates ETG API call on every hotel view | Low |
| Add `policy_struct` | Eliminates ETG API call | Low |
| Add `metapolicy_extra_info` | Complete policy display without API call | Low |
| Skip `deleted` hotels | Correct inventory (no ghost hotels) | Trivial |
| Add `amenity_groups` structured | Proper grouped display | Medium |
| Add `contact` (email/phone) | Contact info without API call | Low |
| Add `room_group_id` | Correct room-rate matching | Low |
| Add `images_ext` | Categorized gallery | Low |
| Fix facts field names | Correct HotelFacts display | Low |
| Synthesize `description` | Enable text/semantic search on description | Low |

**Total: Eliminates ~2-3 ETG API fallback calls per hotel view, fixes room matching, enables proper description search.**
