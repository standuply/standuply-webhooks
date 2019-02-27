# Standuply Webhooks

Standuply will send an HTTP POST request to your endpoint. The event will be in the `Content-Type: application/json` format. Take a look to TypeScript types below to understand the message structure. `WebhookMessage` is the root of message.
```typescript
interface WebhookMessage {
    reportName: string;
    scheduledAt: number; // Unix Timestamp in seconds
    teamId: string;
    subject?: {
        id: string; // trello card id or slack user id
        type: 'task' | 'person' | 'custom';
        title: string;
    };
    isAsyncResults: boolean;
    isReportFinished: boolean;
    // if `isAsyncResults` is false your will receive an array of `WebhookMessageResult`
    result: WebhookMessageResult | Array<WebhookMessageResult>;
}

interface WebhookMessageResult {
    type: 'user-answers' | 'data-request' | 'survey-summary';
    value: WebhookMessageUserAnswers | WebhookMessageRequestResult | WebhookMessageSurveySummary;
}
        
interface WebhookMessageUserAnswers {
    userId: string;
    userName: string;
    type: 'answer-later' | 'timed-out' | 'normal' | 'ignored-by-status' | 'quick-answer'
        | 'video-answer' | 'voice-answer' | 'taking-part-in-another-report' | 'answer-next-time' | 'skipped';
    answers: Array<WebhookRespondentDialogAnswer> | WebhookRespondentDialogQuickAnswer
        | WebhookRespondentDialogVideoAnswer | WebhookRespondentDialogVoiceAnswer | WebhookRespondentsDialogAnswerLater
        | WebhookTakingPartInAnotherReportDialogAnswer | WebhookRespondentsDialogAnswerNextTime
        | WebhookSkippedDialogAnswerData;
}

interface WebhookRespondentDialogAnswer {
    question: string;
    type: 'text' | 'survey' | 'trello-task';
    answer: string;
    answerId?: string; // trello card id for 'trello-task'
}

interface WebhookSkippedDialogAnswerData {
}

interface WebhookRespondentsDialogAnswerLater {
    time?: string; // hh:mm a (Z)
    willAnswerAt?: number; // Unix Timestamp in seconds
}

interface WebhookRespondentsDialogAnswerNextTime {
    time: string; // hh:mm a
}

interface WebhookRespondentDialogQuickAnswer {
    answer: string;
}

interface WebhookRespondentDialogVideoAnswer {
    answer: string; // the url to the video on youtube
}

interface WebhookRespondentDialogVoiceAnswer {
    answer: string; // the url to the video on youtube
}

interface WebhookTakingPartInAnotherReportDialogAnswer {
}

interface WebhookIgnoredByStatusDialogAnswer {
    status: string;
}
        
interface WebhookMessageRequestResult {
    requestName: string;
    attachment: SlackMessageAttachment;
}
        
interface WebhookMessageSurveySummary {
    question: string;
    isAnonymous: boolean;
    counts: Array<{ option: string; count: number; }>;
    imageUrl: string;
}
        
interface SlackMessageAttachment {
    fallback?: string;
    color?: string;
    pretext?: string;
    authorName?: string;
    authorLink?: string;
    authorIcon?: string;
    title?: string;
    titleLink?: string;
    text?: string;
    fields?: Array<{
        title?: string;
        value?: string;
        short: boolean;
    }>;
    imageUrl?: string;
    thumbUrl?: string;
    footer?: string;
    footerIcon?: string;
    ts?: number;
    mrkdwnIn?: Array<'text'|'pretext'|'fields'>;
}
```