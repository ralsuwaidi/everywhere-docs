# Villa Approval Process

Villa Approval Process Documentation

## Overview

The Villa approval process is a crucial workflow in the Everywhere project that ensures all listed villas meet the required standards before they become visible to potential guests. This process involves several stages and actions from both villa owners and administrators.

## Stages

The Villa model has a `review_status` field with the following possible states:

1. `DRAFT` ("DR"): Initial state when a villa is created or being edited
2. `PENDING_REVIEW` ("PR"): Villa has been submitted for review
3. `APPROVED` ("AP"): Villa has been approved by an admin
4. `REJECTED` ("RJ"): Villa has been rejected by an admin

## Process Flow

### 1. Villa Creation

- A villa owner creates a new villa, which is initially in the `DRAFT` state.
- The owner can edit and update the villa details while it's in this state.  


### 2. Submission for Review

When the owner believes the villa is ready for listing, they submit it for review:

```
@action(detail=True, methods=["post"], permission_classes=[IsOwnerOrAdmin], url_path="submit")
def submit_villa(self, request, slug=None):
    villa = VillaService.submit_villa(request.user, slug)
    serializer = VillaListSerializer(villa, context={"request": request})
    return Response(serializer.data, status=status.HTTP_200_OK)

```

The `submit_villa` method in `VillaService` performs the following checks:

- Ensures the user is the owner of the villa or an admin
- Verifies the villa is in `DRAFT` status
- Checks that the villa has a base price set
- Confirms at least one image has been uploaded for the villa  


If all checks pass, the villa's status is changed to `PENDING_REVIEW`.

### 3. Admin Review

Admins can view villas in the `PENDING_REVIEW` state and decide to approve or reject them.  
To approve a villa:

```
@action(detail=True, methods=["post"], permission_classes=[IsAdminUser])
def approve_villa(self, request, slug=None):
    villa = VillaService.approve_villa(slug)
    serializer = VillaListSerializer(villa, context={"request": request})
    return Response(serializer.data, status=status.HTTP_200_OK)

```

The `approve_villa` method in `VillaService`:

- Retrieves the villa by slug
- Checks if the villa is not already approved
- Changes the villa's `review_status` to `APPROVED`  


### 4. Post-Approval

Once a villa is approved:

- It becomes visible in search results for guests
- Bookings can be made for the villa
- The owner can still update the villa, but major changes might require re-approval (this would need to be implemented if desired)  


## API Endpoints

1. Submit Villa for Review:
   - URL: `POST /api/villas/{slug}/submit/`
   - Permission: Villa Owner or Admin
2. Approve Villa:
   - URL: `POST /api/villas/{slug}/approve_villa/`
   - Permission: Admin only

## Permissions

- `IsOwnerOrAdmin`: Allows access to villa owners and admin users
- `IsAdminUser`: Restricts access to admin users only  


## Future Enhancements

Consider implementing the following to improve the approval process:

1. Rejection mechanism with feedback for owners
2. Automatic notifications to owners when their villa status changes
3. Re-submission process for rejected villas
4. Version control for villa details to track changes between approvals

## Notes

- The current implementation doesn't include a specific rejection process. Consider adding this for a more complete workflow. (todo)
- There's no automatic notification system in place. Implementing one could improve the user experience for villa owners. (todo)
