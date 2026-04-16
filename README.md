# EventHub — Event Ticketing API

A backend API for a simplified event ticketing platform built with Django and Django REST Framework. Users can browse events, reserve seats, and cancel reservations.

## How to Run

```bash
cd eventhub
pip install -r requirements.txt
python manage.py makemigrations
python manage.py migrate
python manage.py runserver
```

The API will be available at `http://127.0.0.1:8000/api/`.

## API Endpoints

### Events

| Method | Path                          | Description        |
|--------|-------------------------------|--------------------|
| GET    | /api/events/                  | List all events    |
| POST   | /api/events/                  | Create event       |
| GET    | /api/events/{id}/             | Get single event   |
| PUT    | /api/events/{id}/             | Update event       |
| DELETE | /api/events/{id}/             | Delete event       |
| GET    | /api/events/?status=upcoming  | Filter by status   |
| GET    | /api/events/?venue=mumbai     | Filter by venue    |

### Reservations

| Method | Path                              | Description          |
|--------|-----------------------------------|----------------------|
| GET    | /api/reservations/                | List all reservations|
| POST   | /api/reservations/                | Create reservation   |
| GET    | /api/reservations/{id}/           | Get single reservation|
| GET    | /api/reservations/?event_id=1     | Filter by event      |
| POST   | /api/reservations/{id}/cancel/    | Cancel reservation   |

### Sample Requests

**Create Event — POST /api/events/**
```json
{
    "title": "PyCon India 2025",
    "venue": "NIMHANS Convention Centre, Bangalore",
    "date": "2025-09-20",
    "total_seats": 500,
    "available_seats": 500,
    "status": "upcoming"
}
```

**Reserve Seats — POST /api/reservations/**
```json
{
    "event": 1,
    "attendee_name": "Priya Sharma",
    "attendee_email": "priya@example.com",
    "seats_reserved": 2
}
```

**Cancel Reservation — POST /api/reservations/1/cancel/**
No request body needed.

## Design Decision

**Seat deduction in the serializer's `create()` method:** When a reservation is created, `available_seats` on the Event is decremented inside `ReservationSerializer.create()`. This keeps both the reservation creation and the seat update in a single method, making the logic easy to follow and ensuring seats are always deducted when a reservation is made. In a production system with high concurrency, this would be wrapped in `transaction.atomic()` with `select_for_update()` to prevent race conditions.
