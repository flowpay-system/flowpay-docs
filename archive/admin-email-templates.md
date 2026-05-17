# Admin email templates archived

Status: archived
Source: `flowpay-api/src/services/api/email/templates/admin-notificacao.mjs`

These templates were removed from the runtime backend and kept here only as reference.

## adminAprovacaoTemplate

```js
export const adminAprovacaoTemplate = ({ name, email, approvedAt }) =>
    emailTemplate({
        badge: '✅ Cadastro aprovado',
        badgeColor: 'green',
        title: name,
        body: `
            <table width="100%" cellpadding="0" cellspacing="0"
                   style="background:#0a0a0a;border:1px solid rgba(255,255,255,0.06);border-radius:12px;overflow:hidden;font-size:0.88rem">
                <tr>
                    <td style="padding:12px 18px;color:rgba(255,255,255,0.4);width:38%;border-bottom:1px solid rgba(255,255,255,0.05)">E-mail</td>
                    <td style="padding:12px 18px;border-bottom:1px solid rgba(255,255,255,0.05)">${email}</td>
                </tr>
                <tr>
                    <td style="padding:12px 18px;color:rgba(255,255,255,0.4)">Aprovado em</td>
                    <td style="padding:12px 18px">${approvedAt}</td>
                </tr>
            </table>
        `,
    });
```

## adminRejeicaoTemplate

```js
export const adminRejeicaoTemplate = ({ name, email, reason, rejectedAt }) =>
    emailTemplate({
        badge: '❌ Cadastro rejeitado',
        badgeColor: 'red',
        title: name,
        body: `
            <table width="100%" cellpadding="0" cellspacing="0"
                   style="background:#0a0a0a;border:1px solid rgba(255,255,255,0.06);border-radius:12px;overflow:hidden;font-size:0.88rem">
                <tr>
                    <td style="padding:12px 18px;color:rgba(255,255,255,0.4);width:38%;border-bottom:1px solid rgba(255,255,255,0.05)">E-mail</td>
                    <td style="padding:12px 18px;border-bottom:1px solid rgba(255,255,255,0.05)">${email}</td>
                </tr>
                <tr>
                    <td style="padding:12px 18px;color:rgba(255,255,255,0.4);border-bottom:1px solid rgba(255,255,255,0.05)">Motivo</td>
                    <td style="padding:12px 18px;border-bottom:1px solid rgba(255,255,255,0.05)">${reason}</td>
                </tr>
                <tr>
                    <td style="padding:12px 18px;color:rgba(255,255,255,0.4)">Rejeitado em</td>
                    <td style="padding:12px 18px">${rejectedAt}</td>
                </tr>
            </table>
        `,
    });
```

## Runtime status

- `adminAprovacaoTemplate`: not used in `worker.ts`
- `adminRejeicaoTemplate`: not used in `worker.ts`
- `adminNovoCadastroTemplate`: remains active in backend runtime
