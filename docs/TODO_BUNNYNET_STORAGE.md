# TODO: Add Bunny.net File Storage Support

This document outlines the steps required to add bunny.net as a file storage option for Mastodon Glitch Edition.

## Overview

Bunny.net offers a CDN and cloud storage service that could serve as an alternative to S3, Azure, or Swift for storing media attachments. This integration would provide users with another choice for their file storage backend.

## Background

Currently, Mastodon supports the following file storage options:
- **S3** (AWS S3 and S3-compatible providers) - See lines 38-117 in `config/initializers/paperclip.rb`
- **Swift** (OpenStack Object Storage) - See lines 118-141 in `config/initializers/paperclip.rb`
- **Azure** (Azure Blob Storage) - See lines 142-161 in `config/initializers/paperclip.rb`
- **Filesystem** (local storage) - See lines 163-169 in `config/initializers/paperclip.rb`

Each storage provider has its own configuration block in `config/initializers/paperclip.rb` that is activated via an environment variable (e.g., `S3_ENABLED=true`).

## Implementation Steps

### 1. Research Bunny.net Storage API
- [ ] Review Bunny.net's storage API documentation
- [ ] Determine if Bunny.net is S3-compatible or requires a custom integration
- [ ] Identify required API credentials (API key, storage zone name, region, etc.)
- [ ] Determine if there's an existing Ruby gem for Bunny.net storage
- [ ] Check if Bunny.net supports necessary features:
  - [ ] Object upload/download
  - [ ] Object deletion
  - [ ] Custom headers (Cache-Control, etc.)
  - [ ] Public URL generation
  - [ ] Custom domain/CDN support

### 2. Determine Integration Approach

**Option A: S3-Compatible Mode**
- [ ] Test if Bunny.net storage is S3-compatible
- [ ] If yes, document how to use existing S3 configuration with Bunny.net endpoints
- [ ] Update documentation to include Bunny.net as an S3-compatible provider

**Option B: Custom Integration via Paperclip-Azure Pattern**
- [ ] Identify or create a Paperclip storage adapter for Bunny.net (similar to `jd-paperclip-azure`)
- [ ] Add the gem dependency to `Gemfile`

**Option C: Custom Integration via Fog**
- [ ] Check if fog-core supports Bunny.net
- [ ] If not, evaluate creating a fog provider for Bunny.net

### 3. Add Gem Dependencies (if required)
- [ ] Add Bunny.net Ruby SDK or Paperclip adapter to `Gemfile`
  - Location: After line 21 in `Gemfile` (near other storage gems)
  - Example: `gem 'paperclip-bunnynet', '~> X.X', require: false`
- [ ] Run `bundle install` to update `Gemfile.lock`
- [ ] Ensure the gem is marked as `require: false` to load conditionally

### 4. Update Paperclip Configuration
- [ ] Add new configuration block in `config/initializers/paperclip.rb`
  - Location: After the Azure block (around line 162), before the else clause
  - Pattern to follow:
    ```ruby
    elsif ENV['BUNNY_ENABLED'] == 'true'
      require 'paperclip-bunnynet' # or appropriate gem
      
      Paperclip::Attachment.default_options.merge!(
        storage: :bunnynet,
        bunnynet_credentials: {
          api_key: ENV['BUNNY_API_KEY'],
          storage_zone: ENV['BUNNY_STORAGE_ZONE'],
          region: ENV.fetch('BUNNY_REGION') { 'de' },
        },
        bunnynet_options: {
          # Bunny.net specific options
        }
      )
      
      if ENV.key?('BUNNY_CDN_HOST')
        Paperclip::Attachment.default_options.merge!(
          url: ':bunnynet_cdn_url',
          bunnynet_cdn_host: ENV['BUNNY_CDN_HOST']
        )
      end
    end
    ```
- [ ] Ensure proper file headers are set (Cache-Control, etc.)
- [ ] Configure public/private access controls as appropriate

### 5. Update Environment Configuration Sample
- [ ] Add Bunny.net configuration section to `.env.production.sample`
  - Location: After the Azure section (around line 161)
  - Required environment variables:
    ```bash
    # Bunny.net Storage (optional)
    # The CDN host must allow cross origin request - see the description
    # for S3 storage above.
    # BUNNY_ENABLED=true
    # BUNNY_API_KEY=
    # BUNNY_STORAGE_ZONE=
    # BUNNY_REGION=de
    # BUNNY_CDN_HOST=
    ```
- [ ] Add comments explaining each variable
- [ ] Document the CDN host CORS requirements

### 6. Add Tests
- [ ] Add unit tests for Bunny.net storage configuration loading
  - Pattern to follow: See tests in `spec/lib/mastodon/cli/media_spec.rb` for S3/storage tests
- [ ] Add integration tests if possible (may require mocking Bunny.net API)
- [ ] Test file upload, download, and deletion
- [ ] Test URL generation for public access
- [ ] Test CDN host aliasing if applicable

### 7. Update Documentation
- [ ] Add Bunny.net to the list of supported storage providers in documentation
- [ ] Create a setup guide for Bunny.net storage
  - Include steps to create a Bunny.net account
  - Explain how to obtain API credentials
  - Document required storage zone configuration
  - Explain CDN setup and custom domain configuration
- [ ] Add troubleshooting section for common Bunny.net issues
- [ ] Update `README.md` or create `docs/BUNNY_STORAGE.md`

### 8. Consider Migration Support
- [ ] Evaluate if media migration tools (`lib/mastodon/cli/media.rb`) need updates
- [ ] Test migrating existing media from other storage providers to Bunny.net
- [ ] Document migration procedure if needed

### 9. Security Considerations
- [ ] Ensure API keys are properly handled and not logged
- [ ] Verify CORS configuration for CDN access
- [ ] Test access control (public vs. private files)
- [ ] Review security implications of Bunny.net's API

### 10. Performance Testing
- [ ] Benchmark upload/download speeds
- [ ] Test with various file sizes and types
- [ ] Verify CDN caching behavior
- [ ] Test concurrent uploads

## Files to Modify

### Core Configuration
- `config/initializers/paperclip.rb` - Add Bunny.net storage configuration
- `.env.production.sample` - Add Bunny.net environment variables
- `Gemfile` - Add Bunny.net gem dependency (if needed)

### Documentation
- `README.md` or `docs/BUNNY_STORAGE.md` - Add setup documentation
- `docs/DEVELOPMENT.md` - Update development setup if needed

### Tests
- `spec/lib/mastodon/cli/media_spec.rb` - Add storage tests
- Create new test files as appropriate

## Dependencies

### Required Research
1. Bunny.net Storage API documentation: https://docs.bunny.net/docs/storage-api
2. Bunny.net Ruby SDK (if exists)
3. S3 compatibility status

### Potential Gems
- Existing Paperclip adapter (research required)
- Or implement via fog-core
- Or implement via S3 compatibility layer

## Open Questions

1. **Is Bunny.net S3-compatible?**
   - If yes, this significantly simplifies implementation
   - If no, custom adapter is required

2. **Does a Ruby gem/adapter already exist?**
   - Check RubyGems for bunny.net or bunnycdn packages
   - Check Paperclip plugins

3. **What authentication method does Bunny.net use?**
   - API keys
   - Access tokens
   - Other?

4. **Does Bunny.net support streaming uploads?**
   - Important for large video files

5. **What are the rate limits?**
   - May affect bulk operations

## Success Criteria

- [ ] Users can configure Mastodon to use Bunny.net for file storage via `BUNNY_ENABLED=true`
- [ ] Media uploads work correctly to Bunny.net
- [ ] Media is accessible via CDN URLs
- [ ] Files can be deleted from Bunny.net
- [ ] Configuration is well-documented
- [ ] Tests pass and cover the new functionality
- [ ] No breaking changes to existing storage providers

## References

- Existing S3 implementation: `config/initializers/paperclip.rb` lines 38-117
- Existing Azure implementation: `config/initializers/paperclip.rb` lines 142-161
- Paperclip documentation: https://github.com/kreeti/kt-paperclip
- Mastodon storage documentation: https://docs.joinmastodon.org/admin/config/#cdn

## Notes

- This is a feature addition and should not break existing functionality
- Follow the existing patterns used for Azure and Swift storage
- Ensure backward compatibility
- Consider that this is glitch-soc, a fork of Mastodon - check if upstream has any related work
