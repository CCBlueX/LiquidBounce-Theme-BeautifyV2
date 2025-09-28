<script lang="ts">
    import {flip} from "svelte/animate";
    import {listen} from "../../../../integration/ws";
    import {fly} from "svelte/transition";
    import Notification from "./Notification.svelte";
    import type {NotificationEvent} from "../../../../integration/events";

    interface TNotification {
        animationKey: number;
        id: number;
        title: string;
        severity: string;
        message: string;
    }

    let notifications: TNotification[] = [];

    function addNotification(title: string, message: string, severity: string) {
        const animationKey = Date.now();
        const id = animationKey;

        if (severity === "ENABLED" || severity === "DISABLED") {
            [title, message] = [message, severity === "ENABLED" ? "Module enabled." : "Module disabled."];
        }

        if (severity === "ENABLED" || severity === "DISABLED") {
            const index = notifications.findIndex((n) => n.title === title && n.message === message);
            if (index !== -1) {
                notifications.splice(index, 1);
            }
        }

        notifications = [
            {animationKey, id, title, message, severity},
            ...notifications,
        ];

        setTimeout(() => {
            notifications = notifications.filter((n) => n.id !== id);
        }, 1500);
    }

    listen("notification", (e: NotificationEvent) => {
        addNotification(e.title, e.message, e.severity);
    });
</script>

<div class="notifications">
    {#each notifications as {title, message, severity, animationKey} (animationKey)}
        <div
            animate:flip={{ duration: 200 }}
            in:fly={{ x: 100, duration: 200 }}
            out:fly={{ x: 100, duration: 200 }}
        >
            <Notification {title} {message} {severity}/>
        </div>
    {/each}
</div>
