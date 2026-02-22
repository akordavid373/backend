# PR Description: [Feature] Delegate Claiming Implementation

## Description
This PR implements the delegate claiming functionality for the Vesting Vault system, allowing beneficiaries to set a delegate address that can trigger claim functions on their behalf while funds still go to the beneficiary's cold wallet.

## Changes Made

### 🗄️ Database Model Updates
- **File: `backend/src/models/beneficiary.js`**
  - Added `delegate_address` field to the Beneficiary model
  - Added index for efficient querying of delegate addresses
  - Field is nullable to support optional delegate functionality

### 🔧 Service Layer Implementation
- **File: `backend/src/services/vestingService.js`**

#### New Functions:
- `setDelegate(vault_address, beneficiary_address, delegate_address)` - Sets a delegate for a beneficiary
- `getDelegate(vault_address, beneficiary_address)` - Retrieves current delegate information  
- `removeDelegate(vault_address, beneficiary_address)` - Removes delegate authorization

#### Enhanced Functions:
- `processWithdrawal()` - Enhanced to support delegate authorization
  - Added optional `caller_address` parameter
  - Authorization check: allows beneficiary or delegate to withdraw
  - Returns additional metadata about who performed the withdrawal

### 🌐 API Endpoints
- **File: `backend/src/index.js`**

#### New Routes:
- `POST /api/vaults/:vaultAddress/:beneficiaryAddress/delegate` - Set delegate
- `GET /api/vaults/:vaultAddress/:beneficiaryAddress/delegate` - Get delegate info
- `DELETE /api/vaults/:vaultAddress/:beneficiaryAddress/delegate` - Remove delegate

#### Updated Routes:
- `POST /api/vaults/:vaultAddress/:beneficiaryAddress/withdraw` - Enhanced to support caller address via body or `x-caller-address` header

### 🧪 Test Coverage
- **File: `backend/test/delegate.test.js`**
  - Comprehensive test suite covering all delegate functionality
  - Tests for setting/removing delegates
  - Authorization and security tests
  - Error handling validation

## 📋 Acceptance Criteria
- [x] **set_delegate(vault_id, delegate_address)** - ✅ Implemented with full API endpoints
- [x] **Update claim to allow delegate signature** - ✅ Enhanced withdrawal logic with delegate authorization

## 🔒 Security Features
1. **Authorization Check**: Only beneficiary or authorized delegate can withdraw
2. **Audit Trail**: Withdrawal records include who performed the action
3. **Flexible Input**: Supports caller address via request body or header
4. **Error Handling**: Clear error messages for unauthorized attempts

## 🔄 Backward Compatibility
- All existing functionality remains unchanged
- Delegate functionality is optional (nullable field)
- Existing withdrawal endpoints work without caller address
- No breaking changes to existing API contracts

## 📚 Documentation
- `DELEGATE_IMPLEMENTATION.md` - Complete implementation documentation
- Inline code documentation for all new functions
- API usage examples in documentation

## 🧪 Testing
- Comprehensive test suite with full coverage of new functionality
- Tests for authorization, error handling, and edge cases
- Integration tests for API endpoints

## 📊 Files Changed
- `backend/src/models/beneficiary.js` - Added delegate field
- `backend/src/services/vestingService.js` - Added delegate functions and enhanced withdrawal
- `backend/src/index.js` - Added delegate API endpoints
- `backend/test/delegate.test.js` - Complete test suite
- `DELEGATE_IMPLEMENTATION.md` - Documentation

## 🚀 Usage Examples

### Set a Delegate
```bash
POST /api/vaults/0x123.../0x222.../delegate
{
  "delegate_address": "0x333..."
}
```

### Withdraw as Delegate
```bash
POST /api/vaults/0x123.../0x222.../withdraw
{
  "amount": "100",
  "transaction_hash": "0xabc...",
  "block_number": 12345,
  "caller_address": "0x333..."  # Delegate address
}
```

## 🔗 Related Issues
Closes #13

## 📝 Testing Instructions
1. Run the test suite: `npm test -- delegate.test.js`
2. Test API endpoints manually or with Postman
3. Verify delegate authorization works correctly
4. Test unauthorized access rejection

## ✅ Checklist
- [x] Code follows project style guidelines
- [x] Self-review of the code completed
- [x] Code is properly commented
- [x] Tests added for new functionality
- [x] Documentation updated
- [x] No breaking changes introduced
- [x] Security considerations addressed

---

**This implementation provides a secure, flexible solution for delegate claiming while maintaining the security principle that funds always go to the beneficiary's address.**
