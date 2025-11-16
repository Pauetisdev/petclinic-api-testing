# 🧪 REST API Testing — PetClinic (Postman)

This repository contains the API testing layer (Postman) for the Spring PetClinic REST application. The primary goal is functional validation and automation of backend endpoints using a Postman collection with ordered tests and dynamic data handling.

Quick summary
- Type: Postman collection for integration/functional API tests.
- Technologies: Postman (collection + scripts), JavaScript in Pre-request and Test scripts.
- Requirement: a running instance of the Spring PetClinic REST backend.

---

## 🎯 Testing methodology and core principles

Our approach validates the API contract and data persistence by simulating real user flows and handling dependencies between resources.

1. Data chaining (execution flow)
   - The suite relies on captured variables (for example `createdOwnerId`, `createdPetId`) to maintain the flow: create → use → update → delete.

2. Dynamic data handling and type safety
   - `POST` and `PUT` bodies are generated dynamically in Pre-request Scripts to avoid collisions and allow deterministic checks.
   - Use explicit conversions (e.g. `parseInt`) in assertions when necessary to avoid failures due to `string` vs `number` differences.

3. Order and independence
   - The collection is intended to run sequentially (Postman Runner). Some requests depend on resources created in earlier steps.

---

## 📌 Implemented API endpoints

Only the endpoints validated in the Postman collection are listed below, organized by resource.

### A. Owners (root entity)

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| GET | `/api/owners` | Retrieve all pet owners |
| GET | `/api/owners/{ownerId}` | Get a specific pet owner by ID |
| POST | `/api/owners` | Create owner (captures `createdOwnerId`) |
| PUT | `/api/owners/{ownerId}` | Update owner details |
| DELETE | `/api/owners/{ownerId}` | Delete an owner |
| GET | `/api/owners/{ownerId}/pets/{petId}` | Get a specific pet by ID (hierarchical path) |
| PUT | `/api/owners/{ownerId}/pets/{petId}` | Update pet details (hierarchical path) |
| POST | `/api/owners/{ownerId}/pets` | Add a new pet for an owner (captures `createdPetId`) |
| POST | `/api/owners/{ownerId}/pets/{petId}/visits` | Add a vet visit for a pet (hierarchical) |

### B. Pets (flat path)

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| GET | `/api/pets` | Retrieve all pets (flat path) |
| GET | `/api/pets/{petId}` | Get a specific pet by ID (flat path) |
| PUT | `/api/pets/{petId}` | Update pet details (flat path) |
| DELETE | `/api/pets/{petId}` | Delete a pet (flat path) |

### C. Visits (transaction entity)

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| GET | `/api/visits` | Retrieve all visits |
| GET | `/api/visits/{visitId}` | Get a specific visit by ID |
| POST | `/api/visits` | Create visit (requires `petId` in body; captures `createdVisitId`) |
| PUT | `/api/visits/{visitId}` | Update visit details |
| DELETE | `/api/visits/{visitId}` | Delete a visit |

### D. Vets (veterinarians)

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| GET | `/api/vets` | Retrieve all veterinarians |
| GET | `/api/vets/{vetId}` | Get a vet by ID |
| POST | `/api/vets` | Create a new vet (captures `createdVetId`) |
| PUT | `/api/vets/{vetId}` | Update vet details |
| DELETE | `/api/vets/{vetId}` | Delete a vet |

### E. Pet Types (master data)

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| GET | `/api/pettypes` | Retrieve all pet types |
| GET | `/api/pettypes/{petTypeId}` | Get a pet type by ID |
| POST | `/api/pettypes` | Add a new pet type (captures `createdPetTypeId`) |
| PUT | `/api/pettypes/{petTypeId}` | Update pet type details |
| DELETE | `/api/pettypes/{petTypeId}` | Delete a pet type |

---

## 🛠️ Execution and setup

A running Spring PetClinic REST service is required. Quick options to run it locally:

Windows (using the included wrapper):

```bash
mvnw.cmd spring-boot:run
```

(Use `./mvnw spring-boot:run` on Unix/macOS.)

With Docker (official image):

```bash
docker run -p 9966:9966 springcommunity/spring-petclinic-rest
```

Note: the collection assumes the base path `/petclinic/api` by default (see Postman environment variables).

---

## 🚀 Using the collection in Postman (steps)

1. Import the collection file: `PetClinic_Practice.postman_collection.json`.
2. Create or import an Environment and add the `baseUrl` variable.
   - Example value: `http://localhost:9966/petclinic/api`
3. Ensure the execution order in the Runner respects dependencies (create Owner before Pet, etc.).
4. Run the collection with Postman Runner (sequential mode).

Recommended environment variables

- `baseUrl` — API base URL
- `createdOwnerId`, `createdPetId`, `createdVetId`, `createdPetTypeId`, `createdVisitId` — chaining variables

---

## ✍️ Example Pre-request Script (dynamic data)

A snippet to generate unique data before a POST request in Postman:

```javascript
// Generate a unique name and store it in tempFirstName
const randomSuffix = Math.floor(Math.random() * 10000);
pm.environment.set('tempFirstName', `TestOwner_${randomSuffix}`);
pm.environment.set('tempLastName', 'API');
// Example: request body = {"firstName": pm.environment.get('tempFirstName'), "lastName": pm.environment.get('tempLastName'), ...}
```

Example of a type-safe assertion in Tests:

```javascript
pm.test('Owner id is numeric and matches expected', function () {
  const body = pm.response.json();
  pm.expect(parseInt(body.id)).to.eql(parseInt(pm.environment.get('createdOwnerId')));
});
```

---

## ✅ Best practices and recommendations

- Run with Postman Runner in sequential mode to preserve chaining integrity.
- Clear environment variables between runs if you want idempotent tests.
- Add negative tests (4xx responses) to validate error handling.
- Consider parameterizing the collection to run against different databases (H2/HSQLDB/MySQL/Postgres) for deeper integration tests.

---

## Contributing

If you want to improve the collection or the repository:
1. Fork the repo.
2. Add/edit tests or scripts in the Postman collection.
3. Open a pull request with a clear description of changes.

---

## License
This project inherits the primary repository license (see `LICENSE.txt`).

---

If you want, I can:
- Generate a sample `postman_environment.json` with the required variables.
- Add additional Pre-request / Test examples for negative cases or edge scenarios.

Tell me which of the options above you want me to add next and I will implement it.
