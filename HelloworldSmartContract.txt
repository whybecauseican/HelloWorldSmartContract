// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract SimpleSubscriptionService {
    address public owner;

    struct Subscription {
        uint256 id;
        address subscriber;
        uint256 startDate;
        uint256 amountPaid;
        bool isActive;
    }

    uint256 public subscriptionCount;
    mapping(uint256 => Subscription) public subscriptions;

    event SubscriptionCreated(uint256 subscriptionId, address subscriber, uint256 amountPaid);
    event SubscriptionCancelled(uint256 subscriptionId, address subscriber);
    event SubscriptionRefunded(uint256 subscriptionId, address subscriber, uint256 refundAmount);

    constructor() {
        owner = msg.sender; // Set the deployer as the owner
    }

    // Function to start a subscription
    function startSubscription() public payable {
        require(msg.value > 0.01 ether, "Minimum subscription amount is 0.01 ether.");
        
        subscriptionCount++;
        subscriptions[subscriptionCount] = Subscription({
            id: subscriptionCount,
            subscriber: msg.sender,
            startDate: block.timestamp,
            amountPaid: msg.value,
            isActive: true
        });

        emit SubscriptionCreated(subscriptionCount, msg.sender, msg.value);
    }

    // Function to cancel a subscription (only the subscriber can cancel)
    function cancelSubscription(uint256 subscriptionId) public {
        Subscription storage subscription = subscriptions[subscriptionId];
        
        require(subscription.id == subscriptionId, "Subscription does not exist.");
        require(subscription.subscriber == msg.sender, "Only the subscriber can cancel.");
        require(subscription.isActive, "Subscription is already cancelled.");
        
        subscription.isActive = false;

        emit SubscriptionCancelled(subscriptionId, msg.sender);
    }

    // Function to refund a subscription (only owner can refund an active subscription)
    function refundSubscription(uint256 subscriptionId) public {
        require(msg.sender == owner, "Only the owner can refund subscriptions.");

        Subscription storage subscription = subscriptions[subscriptionId];
        require(subscription.id == subscriptionId, "Subscription does not exist.");
        require(subscription.isActive, "Cannot refund an inactive subscription.");

        uint256 refundAmount = subscription.amountPaid;
        subscription.isActive = false;

        (bool success, ) = payable(subscription.subscriber).call{value: refundAmount}("");
        require(success, "Refund transfer failed.");

        emit SubscriptionRefunded(subscriptionId, subscription.subscriber, refundAmount);
    }

    // Function to get the total active subscriptions
    function getActiveSubscriptionCount() public view returns (uint256) {
        uint256 activeCount = 0;

        for (uint256 i = 1; i <= subscriptionCount; i++) {
            if (subscriptions[i].isActive) {
                activeCount++;
            }
        }

        return activeCount;
    }

    // Fallback function to receive Ether
    receive() external payable {}
}
