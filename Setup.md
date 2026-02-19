# Setting Up PestScan for PC40

This guide walks you through configuring a PestScan account to integrate with the PC40 API.

## Prerequisites

Before you begin, ensure that:

- You have an active PestScan account with admin access
- The **Permanent Monitoring** module is enabled on your account
- You have at least one customer and location configured

---

## Step 1: Create a PC40 Profile

1. Navigate to **General Settings** (cog wheel icon in the main navigation bar)
2. Select **Permanent Monitoring** from the menu
3. Open the **Profiles** tab and click **New**
4. Configure the profile:
    - **Description**: Enter a descriptive name (e.g., `PC40`)
    - **Type**: Select `PC40` from the dropdown
5. Click **Save**
6. Copy the **PCO-ID** — this value is required for API authentication headers

> **Note**: The PCO-ID uniquely identifies your profile and must be included in API requests.

---

## Step 2: Configure the Customer

1. Navigate to your customer:
    - Go to **Equip Company** → **Customers** → Select your customer
2. Verify you are on the **customer page** (not a location)
    - If you see a "Back to customer" button in the top right, click it
3. Open the **Settings** tab
4. Under **Display on this customer's site**, enable **Permanent Monitoring**
5. Click **Save**

---

## Step 3: Configure the Location

1. Navigate to the location that will use PC40
2. Open the **Inspections** tab
3. Select the **Permanent Monitoring** sub-tab
4. Configure the following:
    - **Type**: Select the PC40 profile you created in Step 1
    - **Provider ID** _(optional)_: Enter a unique identifier for this location

> **Note**: The Provider ID is used as the `LocationId` in API calls. If provided, this ID links traps to their corresponding location in PestScan. Use a unique value such as a GUID (e.g., `550e8400-e29b-41d4-a716-446655440000`).
>
> If you leave the Provider ID empty, traps that send events without a `LocationId` can be manually assigned to locations using the **Trap Assignment** feature (see Step 4).

5. Click **Save**

---

## Step 4: Assign Unlinked Traps (Optional)

If traps send events to the API without a `LocationId`, you can manually assign them to locations:

1. Navigate to **General Settings** (cog wheel icon in the main navigation bar)
2. Select **Permanent Monitoring** from the menu
3. Open the **Trap Assignment** tab
4. You will see a list of traps that have sent events without an associated location
5. For each trap:
    - Select the target **Location** from the dropdown
    - Click **Assign**
6. The trap will now be linked to the selected location

> **Tip**: You can also use this feature to reassign traps to different locations at any time.

---

## Verification

Your location is now configured to receive PC40 events. To verify the setup:

1. Confirm the PCO-ID is available in your profile settings
2. Ensure the Provider ID is set on the location, or assign traps via **Trap Assignment**
3. Test the API connection using the authentication endpoint (see [API Documentation](README.md))

---

## Troubleshooting

| Issue                                  | Solution                                                                |
| -------------------------------------- | ----------------------------------------------------------------------- |
| Cannot see Permanent Monitoring option | Make sure your account has the permanent monitoring module                  |
| Events not being received              | Verify Provider ID is set, or assign traps manually via Trap Assignment |
| Traps not appearing in Trap Assignment | Ensure the trap has sent at least one event to the API                  |

---

## Next Steps

- Review the [API Documentation](README.md) for endpoint details
- Set up authentication using the Login endpoint
- Configure your traps to send events to the PC40 API
