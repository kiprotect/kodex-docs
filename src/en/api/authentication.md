# Authentication

The Kodex API can use various authentication schemes and is compatible with multiple external authentication providers. The SaaS version of the API available at `api.kiprotect.com` uses token-based authentication.

## Role-Based Access Management

The Kodex API provides role-based access controls for all objects.
An **API user** belongs to one or more **organizations** and has one or more **roles** in those organizations.
An **API object**, for example a project, stream or data source either belongs to, or is its own **role object**.
Role objects map specific object roles (that e.g. grant writing or reading rights to an object) to organization roles.
Hence, users can access and work with objects based on their assigned organization roles.
