### 책임 연쇄<i>Chain of Responsibility</i>

```js


// 의사 코드 pseudo code
export interface ComplaintListener {
	IsAbleToResolveComplaint(complaint: Complaint): boolean;
	ListenToComplaint(complaint: Complaint): string;
}

var Complaint = (function() {
	function Complaint() {
		this.ComplaintParty = '';
		this.ComplaintAbout = '';
		this.Complaint = '';
	}
	return Complaint;
})();

var ClerkOfTheCount = (function() {
	function ClerkOfTheCount() {}
	
	ClerkOfTheCount.prototype.IsAbleToResolveComplaint = function(complaint) {
		return false;
	};
	
	ClerkOfTheCount.prototype.ListenToComplaint = function(complaint) {
	  return '';
	};
	
	return ClerkOfTheCount;
})();

var King = (function() {
	function King() {}
	King.prototype.IsAbleToResolveComplaint = function(complaint) { return false; }
	King.prototype.ListenToComplaint = function(complaint) { return ''; }
	return King;
})();


var ComplaintResolver = (function() {
	function ComplaintResolver() {
		this.complaintListeners = new Array();
		this.complaintListeners.push(new ClerkOfTheCount());
		this.complaintListeners.push(new King());
	}
	ComplaintResolver.prototype.ResolveComplaint = function(complaint) {
		for (var i = 0; i < this.complaintListeners.length; i++ ) {
			if (this.complaintListerners[i].IsAbleToResolveComplaint(complaint)) {
				return this.complaintListeners[i].ListenToComplaint(complaint);
			}
		}
	};
	return ComplaintResolver;
})();

```
