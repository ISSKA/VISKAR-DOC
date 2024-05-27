API
===

The API defines interaction between client and server applications and
focuses on the REST philosophy. The following conventions have been used:

-   Resources use names in plural (e.g. “/projects”)

-   Type definitions use optional fields to denote which fields must be sent in a request (partial request) and which are sent to the client (complete object)

-   After updating or creating an object (PUT/POST), the object is returned in its current state.

-   Objects are generally allocated a top-level resource to avoid deeply nested routes, and to allow flexibility later on

-   URLs for a single resource always end without a slash, while collections always have one (e.g. “/projects/1” and “/projects/”)
    
Relationships
--------------

-   Related resources may be accessible via a subresource (e.g.“/projects/[id]/users”)
    
-   Relationships are modified by PATCHing the updated parent ID to the affected resource.

    Example: In the hydrostratigraphic column, move formation 9 to unit 11
    
        PATCH /formations/9
        Body:   { newParentId: 11 }
